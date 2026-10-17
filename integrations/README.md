# Integrações

Adaptadores futuros: n8n, Groq, Supabase, Telegram e WhatsApp/Evolution API. Nenhuma integração ativa.

API e módulos funcionam manualmente antes de depender de canais. Cada adaptador deve mapear identidade e contexto, validar entrada, deduplicar eventos, limitar dados e registrar falha sem segredos. O piloto comercial aceita apenas uma identidade autorizada; demais entradas são bloqueadas. Não contatar terceiros como parte de testes.
