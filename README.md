# NÚCLEO — NEXUS / ANIMUS

Base versionada da central operacional da Connecta.ai.

**Estado:** estrutura e especificação inicial. Ainda não há aplicação executável, banco, autenticação ou integrações ativas.

## Objetivo

Operar empresas por uma interface simples e conversacional: NEXUS organiza os registros e fluxos; ANIMUS interpreta solicitações e propõe ações; NEXUS PULSE organiza abertura, repasse e fechamento do dia.

Prioridade: QG Ferreira e PULSE, seguidos pelo comercial da Connecta.ai. Ferreira é parceiro, com contexto e permissões separados das empresas próprias.

## Comece aqui

- [Arquitetura e limites](docs/arquitetura.md)
- [Plano de implementação e critérios de aceite](docs/plano-implementacao.md)
- [Rotina PULSE](docs/pulse.md)
- [Operação privada](docs/operacao-privada.md)

## Organização

| Pasta | Responsabilidade |
| --- | --- |
| apps/web | Interface desktop/mobile |
| apps/api | Autorização e casos de uso |
| modules/core | Contextos, identidade, permissões e auditoria |
| modules/crm | Contatos e oportunidades |
| modules/operations | Tarefas, serviços e movimentos do Ferreira |
| modules/finance | Receitas, despesas, recebimentos e repasses |
| modules/pulse | Rotina e pendências do dia |
| modules/animus | Conversação, propostas de ação e handoff |
| integrations | Adaptadores para canais e automação |
| infra | Configuração de execução local e persistência |
| tests | Cenários de validação futuros |

Cada pasta contém um README com seu contrato inicial; não representa funcionalidade implementada.

## Privacidade

O código deste repositório é público. Dados operacionais devem permanecer em ambiente privado, fora do Git. A execução planejada é local, vinculada a 127.0.0.1; publicação, túneis e acesso remoto não fazem parte desta base. Não há deploy automático.

Copie futuramente `.env.example` para `.env` somente no ambiente local. O arquivo é uma referência de configuração, sem consumidor implementado. Nunca publique dados reais de clientes, estoque, vendas, conversas, gravações ou credenciais.

## Próximo incremento

Implementar uma fatia completa do PULSE: selecionar contexto autorizado → cadastrar tarefa → registrar andamento → abrir pendência de repasse → fechar o dia preservando pendências. Consulte o plano para dependências e aceite.

## Evolução

Mudanças pequenas e revisáveis; registrar novas decisões em docs antes de ampliar módulos. Reutilizar o núcleo em vez de criar CRM ou financeiro por empresa. Automações e IA entram depois do fluxo manual validado.
