# Arquitetura inicial

## Decisão

Começar como monólito modular em um único repositório: interface, API e módulos de domínio. Sem microserviços, filas ou infraestrutura adicional até existir necessidade demonstrada.

NEXUS é o sistema de registro e operação; ANIMUS é a camada conversacional; PULSE coordena o dia a partir dos mesmos registros. O painel e o chat chamam os mesmos casos de uso da API.

## Fluxo

1. Usuário autenticado seleciona um contexto permitido.
2. API resolve identidade, contexto e permissão para a ação.
3. Um caso de uso valida dados e aplica a regra do módulo.
4. Persistência grava a alteração e sua auditoria na mesma transação quando aplicável.
5. Painel e PULSE consultam os registros atualizados; integrações recebem apenas o necessário.

ANIMUS acessa ferramentas autorizadas da API, nunca SQL arbitrário ou credenciais do navegador. Conteúdo de mensagens, documentos e RAG é dado não confiável e não redefine permissões.

## Limites dos módulos

| Módulo | É responsável por | Evita duplicar |
| --- | --- | --- |
| core | Contextos, usuários, vínculos, autorização, auditoria | Cadastros e regras de acesso |
| crm | Contatos, oportunidades e histórico comercial | Cliente em cada serviço |
| operations | Tarefas, ordens de serviço e movimentos de estoque | Caixa ou CRM próprios |
| finance | Títulos, recebimentos, despesas e repasses | Cobrança em cada operação |
| pulse | Visão do dia, checkpoints e pendências | Outro cadastro de tarefas |
| animus | Intenção, consulta, proposta, confirmação e handoff | Regras financeiras e operacionais |

Dependências: core é a base; crm, operations e finance usam core e referências explícitas entre registros; pulse consulta esses módulos e registra checkpoints; animus chama casos de uso. Integrações dependem dos contratos da API, não o contrário.

## Dados e isolamento — requisitos a implementar

- Contexto operacional identificado por company_id. Connecta.ai e Ferreira possuem contextos distintos; parceria não concede acesso automático.
- Todos os registros operacionais carregam company_id. A API verifica vínculo e ação a cada leitura e escrita; não confia no seletor de tela.
- Referências entre contato, oportunidade, tarefa, OS, movimento e título devem pertencer ao mesmo contexto, com integridade no banco.
- Contatos são deduplicados dentro do contexto, por identificador normalizado quando disponível; nenhum cadastro global expõe dados entre empresas.
- Visão geral do proprietário agrega apenas contextos explicitamente autorizados. Perfis iniciais: proprietário/admin e operacional; comercial, financeiro e afiliado entram conforme necessidade.
- Persistência futura usa políticas de acesso por contexto, além da validação na API. Chaves privilegiadas ficam no servidor.
- Auditoria mínima: ator, contexto, ação, entidade, data, resultado e identificador de correlação. Não registrar segredos ou transcrições integrais por padrão.
- Valores em centavos inteiros e moeda BRL. Datas persistidas com fuso; rotina diária interpretada em America/Sao_Paulo.
- Operações repetíveis usam chave de idempotência com unicidade por contexto. Repetir uma conclusão/recebimento não duplica título ou baixa.

Entidades iniciais: contexto, usuário/vínculo, contato, oportunidade, tarefa, checkpoint e evento de auditoria. OS, produto, movimento de estoque, título, recebimento, repasse e sessão de conversa entram nas respectivas etapas.

## ANIMUS

Papéis Geral, Financeiro e Sentinela são rotas da mesma camada, com permissões específicas; não são três bancos nem três painéis duplicados. Começar pelo Geral.

Consulta → proposta estruturada → validação → confirmação quando houver escrita → execução → resultado auditado. Registrar confirmação vinculada à ação; retries não reaplicam a mudança. Cancelamento ou falha não vira sucesso.

Nunca inventar preço, prazo, estoque, horário ou pagamento. Ausência de informação gera pergunta ou encaminhamento humano. Handoff preserva resumo, contexto e pendências; suspende ações automáticas da sessão até retomada explícita.

## Tecnologia

Direção inicial: TypeScript, React/Vite na web, API Node e PostgreSQL. Validar versões e dependências no primeiro incremento executável; nenhum runtime foi instalado nesta entrega. Supabase pode prover PostgreSQL/autenticação; n8n, Groq, RAG, Telegram e WhatsApp entram por adaptadores depois do MVP manual. Escolher serviço externo não autoriza envio de dados nem publicação.

## Interface

Dark, preto/cinza/branco e azul nos destaques. Seletor de contexto, navegação lateral no desktop e inferior no mobile, ação “+ Novo” e entrada ANIMUS. Uma função tem um local principal; PULSE mostra atalhos e pendências, sem recriar módulos.
