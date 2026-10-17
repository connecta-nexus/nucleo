# n8n

Espaço para workflows sanitizados após validar o fluxo manual. Exportar somente estrutura sem credenciais, payloads reais, históricos, IDs de contas ou URLs privadas.

Workflows chamam a API com escopo definido; não duplicam regras de negócio. Planejar idempotência, timeout, retomada e encaminhamento humano antes de ativar. Nenhum workflow incluído.
