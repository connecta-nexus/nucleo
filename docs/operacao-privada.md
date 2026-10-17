# Operação privada

## Estado atual

Este repositório é público e contém apenas estrutura e documentação. Não há serviço rodando nem proteção de acesso implementada. .gitignore reduz inclusões acidentais, mas não remove segredos já versionados nem protege uma aplicação em execução.

## Padrão para implementação

- Desenvolvimento e demonstração em 127.0.0.1; PC do QG é estação de trabalho, sem exigir servidor dedicado.
- Sem GitHub Pages, deploy automático, túnel público ou exposição de portas.
- Mobile remoto exige projeto posterior de acesso privado autenticado; rede local também exige autenticação e autorização.
- Dados, anexos, backups, logs, sessões e credenciais ficam fora do Git. Exemplos usam somente dados sintéticos.
- .env.example contém apenas opções locais e campos vazios. Integração fica desativada até possuir configuração validada.
- Segredos ficam no servidor; nunca no bundle web ou export de workflow. Chaves privilegiadas não chegam ao cliente.
- Backups privados devem ter restauração validada antes do uso operacional. Definir retenção e acesso aos logs ao implementar persistência.
- Uso de provedor externo exige verificar destino dos dados e limitar o payload ao necessário.

## Verificação antes de cada commit

Revisar diff, arquivos novos e exports de automação. Remover nomes/telefones de clientes, endereços, vendas reais, tokens, endpoints privados e dados de pagamento. Arquivos ignorados não devem ser adicionados com força.

Se houver credencial exposta: revogar/rotacionar, identificar alcance e corrigir histórico quando necessário. Apagar apenas o arquivo atual não elimina exposição anterior.

## Antes do primeiro uso real

Validar autenticação, acesso por contexto, restauração de backup, auditoria e erros sem vazamento de dados. Essa validação é requisito futuro, não uma garantia entregue pela estrutura inicial.
