# Aviso de erro por e-mail

O arquivo [`aviso_erro.json`](aviso_erro.json) é um workflow de tratamento de falhas do n8n. Quando um workflow associado falha em uma execução automática, o **Error Trigger** inicia este fluxo e o nó **Gmail** envia um aviso ao responsável.

## O que o aviso contém

- Horário da falha no fuso `America/Sao_Paulo`.
- Nome do workflow, nó em que a execução parou e ID da execução, quando esses campos estiverem disponíveis.
- Orientação para consultar os detalhes no histórico do n8n.

O e-mail **não inclui a mensagem bruta do erro**, o payload da execução, dados de clientes, stack trace ou credenciais. Esses dados podem aparecer em erros de integrações e devem ser consultados somente no ambiente do n8n por quem tem acesso autorizado. O nome do workflow e o ID da execução ainda são informações operacionais enviadas por e-mail; evite colocar dados de clientes no nome dos workflows.

## Configuração

1. Importe `aviso_erro.json` no n8n.
2. No nó **Send a message**, configure sua credencial Gmail e preencha **Send To** com o endereço autorizado a receber os alertas. O JSON distribuído não traz destinatário nem credencial.
3. Abra as configurações do workflow que deseja monitorar e selecione este fluxo em **Error workflow**. Repita para os demais workflows desejados.
4. Faça um teste com uma falha controlada em uma execução **automática**. O Error Trigger não é disparado por uma execução manual do workflow monitorado.

O fluxo de aviso não corrige nem reinicia a execução que falhou. Consulte o histórico do n8n para investigar a causa. Consulte também a [documentação oficial do Error Trigger](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.errortrigger/).

## Revisão de dados expostos

Antes desta revisão, o JSON continha um destinatário preenchido, referências à credencial Gmail e o identificador da instância n8n. Esses campos foram removidos. A expressão original usava `$json.mensagem`, campo que não faz parte do formato padrão do Error Trigger; o aviso agora lê apenas os campos operacionais documentados.
