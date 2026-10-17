# Primeiro plano de implementação

Estado em 17/10/2026: somente a etapa 0 entregue. As demais são propostas ordenadas, sem estimativas ou integrações consideradas prontas.

| Etapa | Entrega | Critério de aceite |
| --- | --- | --- |
| 0 — Base | README, módulos e decisões versionados | Arquivos disponíveis na main; nenhum dado operacional ou segredo incluído |
| 1 — PULSE manual | Web/API local, banco, acesso e contextos, tarefas e checkpoints | Usuário autorizado abre o dia, registra tarefa, atualiza estado e fecha o dia; dados persistem após reiniciar; pendência continua visível no dia seguinte |
| 2 — Ferreira | Cadastro manual de produto, entradas/saídas e pendência de pagamento | Movimento tem quantidade, ator e contexto; saída sem pagamento aparece no repasse; reenvio não duplica movimento; estoque insuficiente é rejeitado |
| 3 — Connecta.ai | Contatos, funil, próxima ação e proposta registrada | Lead percorre Novo lead → Qualificação → Proposta → Negociação → Ganho/Perdido; follow-up aparece no PULSE sem duplicar contato/tarefa |
| 4 — Financeiro | Títulos, recebimentos e fechamento/repasse | Venda a receber e recebimento são distintos; pagamento parcial preserva saldo; repetição não duplica baixa; estorno mantém trilha |
| 5 — ANIMUS Geral | Consulta e ação com confirmação, memória por contexto e handoff | Chat usa as mesmas regras da tela; ação cancelada não escreve; sem fonte o agente não informa preço/estoque; handoff suspende automação |
| 6 — Adaptadores | n8n e canal piloto autorizado | Entrada duplicada produz um único efeito; falha fica rastreável e permite retomada; piloto aceita apenas identidade autorizada |
| 7 — Expansão | Lava Já!, Segurança, CONNECTADOS e Sabores Di’Casa | Reutilizam núcleo e passam por validação própria antes de ativar |

## Primeiro incremento executável: etapa 1

1. Fixar versões e dependências, scripts de desenvolvimento e configuração local; bind em 127.0.0.1.
2. Criar migrações para contexto, usuário/vínculo, tarefa, checkpoint e auditoria; usar dados sintéticos no desenvolvimento.
3. Implementar autenticação e autorização na API, incluindo leitura, escrita e referências entre contextos.
4. Entregar seleção de contexto, lista de tarefas e abertura/repasse/fechamento no desktop e mobile.
5. Executar cenários de isolamento, persistência, permissões e transição de dia. Documentar comandos e resultado.
6. Validar o fluxo manual com o responsável antes de adicionar IA ou canais externos.

## Cenário de demonstração

Com dados sintéticos: abrir PULSE no contexto Ferreira → cadastrar tarefa de conferir um item → registrar pendência → preparar repasse das 13h → alternar para Connecta.ai e registrar follow-up da tarde → fechar às 17h. O resumo geral contém somente contextos autorizados; o usuário exclusivo do Ferreira não acessa o follow-up da Connecta.ai.

## Regras por operação

- **Ferreira:** parceiro separado. Cadastro e conferência manuais de estoque; nenhuma catalogação de vídeo incluída. Repasse distingue saídas, recebimentos e valores pendentes.
- **Connecta.ai:** foco em automação WhatsApp; captação → qualificação → handoff → estudo → proposta/MVP → entrega. Canal WhatsApp fica para etapa posterior.
- **Lava Já!:** cliente → serviço → valor validado → executar → concluir → receber. Conclusão cria título uma única vez; receber faz a baixa.
- **Connecta Segurança:** preparar contratos, tarefas e alertas; gravações e credenciais de câmeras permanecem fora do Git.
- **CONNECTADOS:** afiliado separado de parceiro; 20% de cada recebimento elegível da indicação, incluindo recorrência e adicionais, com ajuste por estorno e sem duplicação; 10 vagas fundadoras. Ativar somente após atribuição e financeiro validados.
- **Sabores Di’Casa:** manter “Em breve”. Preparar futura operação de salgados à tarde e pizza por pedaço à noite; sem presumir preço de pizza, horário exato ou pedidos ativos.

## Pronto para avançar

Cada etapa exige fluxo manual utilizável, falhas visíveis, isolamento validado e instruções reproduzíveis. Não declarar recurso pronto apenas por haver pasta ou tela. Não ampliar visibilidade, ativar canal ou automatizar contato externo como efeito colateral de implementar o núcleo.
