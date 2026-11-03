# ANIMUS + NEXUS OFFICE — operação online

Este documento atualiza o núcleo para refletir a integração real que já existe entre o
NEXUS OFFICE, n8n, Evolution API, Railway e Cloudflare.

## Arquitetura operacional

```
WhatsApp / Site
      |
      v
Evolution API (Railway)
      |
      v  MESSAGES_UPSERT + x-connecta-ingress-key
n8n — CONNECTA | ANIMUS v1 + SENTINELA
      |
      +--> Core ANIMUS (tenant, contexto, memória, políticas)
      +--> Connecta API / ERP Ferreira
      +--> Evolution API (resposta)
      |
      v
Cloudflare Gateway / NEXUS OFFICE
      |
      +--> painel desktop
      +--> resumo mobile
      +--> auditoria e aprovação
```

O navegador nunca fala diretamente com n8n, Evolution ou Railway. O Gateway executa
as chamadas server-side e remove segredos da interface.

## Estado conhecido da operação

- n8n: Railway, serviço `Primary`, domínio
  `primary-production-db42.up.railway.app`.
- Workflow de entrada: `CONNECTA | ANIMUS v1 + SENTINELA`,
  id `t5cSCfurEFuhRhvm`.
- Evolution: Railway, instância `animus01` e instância legada `animus`.
- Evento de entrada: `MESSAGES_UPSERT`.
- Follow-up: workflow separado; falhas precisam aparecer no Office como fila
  retryable, sem apagar histórico.
- Ferreira: tenant separado; nunca recebe acesso implícito aos dados da Connecta.

IDs e URLs são configuração operacional, não segredo. Tokens, chaves, credenciais e
conteúdo de clientes ficam somente em Railway Variables, Cloudflare Secrets e n8n
Credentials.

## Core e roteamento

Cada evento é normalizado antes de qualquer resposta:

```ts
type AnimusEvent = {
  tenant: 'connecta' | 'ferreira';
  channel: 'whatsapp' | 'site' | 'office';
  conversationId: string;
  contact: { phone: string; name?: string };
  message: { id: string; text: string; receivedAt: string };
  source?: 'site' | 'ad' | 'erp' | 'manual';
  idempotencyKey: string;
};
```

O tenant é resolvido pela instância WhatsApp, pelo contexto de origem e pelo cadastro
autorizado. Se houver ambiguidade, o Animus pergunta ou encaminha para humano; ele não
escolhe uma empresa por palpite.

## Animus administrador

O Animus administrador pode ler estado e propor mudanças. Escritas seguem uma máquina
de estados:

1. **draft** — alteração preparada e diff gerado;
2. **test** — teste sintético, sem envio para cliente;
3. **approval** — pessoa autorizada confirma;
4. **publish** — n8n/Cloudflare recebe a mudança;
5. **verify** — healthcheck, execução de prova e registro da versão;
6. **rollback** — volta para a última versão saudável.

Tools permitidas:

- saúde e últimas execuções;
- listar workflows e versões;
- testar webhook com payload sintético;
- criar alteração em branch;
- reprocessar uma execução idempotente;
- ativar/desativar um workflow com confirmação;
- atualizar prompt, tool ou regra em rascunho;
- criar follow-up, handoff e agendamento.

Tools bloqueadas:

- apagar workflows, credenciais, bancos, volumes ou histórico;
- revelar ou substituir segredos;
- enviar mensagem em massa;
- publicar produção sem aprovação;
- alterar permissões, domínios ou billing.

Toda alteração registra ator, tenant, recurso, diff, motivo, aprovação, versão e
resultado. O agente nunca executa SQL arbitrário nem interpreta texto de lead como
permissão.

## Contrato do Office

O Gateway deve expor uma API pequena e estável:

- `GET /v1/office/summary` — saúde, alertas e próxima ação;
- `GET /v1/office/conversations?tenant=...` — conversas e handoffs;
- `GET /v1/office/followups` — fila, atraso e última tentativa;
- `GET /v1/office/workflows` — estado e versão publicada;
- `POST /v1/office/workflows/:id/test` — teste sintético;
- `POST /v1/office/change-requests` — preparar alteração;
- `POST /v1/office/change-requests/:id/approve` — aprovar/publicar;
- `POST /v1/office/change-requests/:id/rollback` — rollback;
- `GET /v1/office/stream` — SSE para desktop e celular.

O resumo mobile deve trazer somente: alertas críticos, leads quentes, conversas
aguardando humano, follow-ups atrasados, workflows com erro e próxima ação.

## Segurança e não destruição

- RBAC por usuário e `company_id`; seleção visual nunca é autorização.
- Idempotência em entrada, resposta, follow-up, agendamento e recebimento.
- Dry-run obrigatório antes de qualquer mudança de tool.
- Aprovação humana para publicação e toda ação externa representacional.
- Retenção mínima de dados; não registrar tokens nem transcrições completas por padrão.
- Rollback sempre disponível para prompt, tool, workflow e configuração de canal.

## Próxima fatia executável

1. Criar os contratos compartilhados em `contracts/`.
2. Implementar o Gateway server-side com healthchecks n8n/Evolution e SSE.
3. Adicionar a tela “ANIMUS Control” no Office consumindo somente o Gateway.
4. Incluir testes sintéticos Connecta/Ferreira, fromMe, grupos, duplicidade e handoff.
5. Publicar em ambiente privado; só depois ligar produção com aprovação.

Esta branch é documentação e contrato inicial. Ela não inclui segredos nem muda produção.
