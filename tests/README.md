# Validação futura

Não há código executável ou testes automatizados nesta base. Ao implementar o MVP, cobrir cenários de comportamento:

- Usuário de Ferreira não lê, altera nem referencia registros da Connecta.ai, inclusive via API direta.
- Ação negada não altera estado; operações autorizadas geram auditoria.
- Tarefa e checkpoints persistem após reinício; fechamento não elimina pendências.
- Data local e virada de dia respeitam America/Sao_Paulo.
- Movimento ou recebimento reenviado produz um único efeito; pagamento parcial mantém saldo.
- ANIMUS cancelado não escreve; conteúdo externo não altera permissões; handoff suspende ações.

Priorizar esses cenários conforme cada etapa for implementada. Verificar desktop e mobile no primeiro fluxo utilizável.
