# FIAT — Agência de viagens com mensagem e IA

Este diretório contém o workflow n8n [`02_FIAT_Agencia_Com_IA.json`](02_FIAT_Agencia_Com_IA.json). Ele recebe um pedido de viagem em texto livre, usa IA **apenas para extrair os dados informados** e aplica regras do próprio workflow para validar o pedido e consultar um **catálogo fictício**. Não consulta preços ou disponibilidade reais, não reserva viagens e não envia mensagens automaticamente.

## Entrada e extração

O formulário **Receber mensagem** exige o campo `mensagem`. O nó **Extrair dados da mensagem** usa um modelo de chat conectado ao n8n e um schema que espera:

| Campo extraído | Significado |
| --- | --- |
| `origem`, `destino` | Cidades mencionadas explicitamente. |
| `data_ida`, `data_volta` | Datas completas com dia, mês e ano, normalizadas para `AAAA-MM-DD`. |
| `adultos`, `criancas` | Quantidades de viajantes. Ausência de menção a crianças fica `null`; zero só quando o texto diz explicitamente que não haverá crianças. |
| `orcamento_valor` | Valor numérico do orçamento, sem conversão de moeda. |
| `orcamento_tipo` | `total`, `por_pessoa` ou `null` quando o texto não permite decidir. |
| `moeda` | `BRL` para reais, outra moeda mencionada ou `null` se ausente. |
| `ambiguidades` | Contradições ou informações presentes, mas incertas. |

O prompt do extrator orienta a não inventar datas, valores ou quantidades, a não completar o ano com a data atual e a tratar o texto recebido como **dados**, não como instruções para mudar o workflow. O modelo configurado no JSON é `gpt-5-mini`; selecione uma credencial e confirme no n8n se esse modelo está disponível para sua conta.

## Como o fluxo funciona

1. **Receber mensagem:** coleta o texto do pedido.
2. **Extrair dados da mensagem:** produz os campos estruturados. **Preservar mensagem e campos** mantém o texto original junto do resultado para a validação e a apresentação.
3. **Conferir os dados:** valida as datas, a ordem ida/volta, de 1 a 20 adultos, a quantidade de crianças, moeda e orçamento. Ausências e ambiguidades geram perguntas específicas. O fluxo não presume que “dois adultos” signifique “sem crianças”.
4. **Dados suficientes?:** só permite consultar o catálogo quando não há pendências. Viagens com crianças bloqueiam a consulta automática e geram um rascunho pedindo consulta manual, pois não estão cobertas pelo catálogo de demonstração.
5. **Catálogo fictício - 3 opções** e **Comparar as condições:** comparam cidades, datas, viajantes, moeda e orçamento. A IA não escolhe pacotes nem calcula preços.
6. **Encontrou opção?:** produz um rascunho com as opções compatíveis ou informa que não há opção. **Pedir informação faltante** produz perguntas quando o pedido ainda não pode ser comparado.
7. **Resultado na página:** exibe os campos extraídos, as regras aplicadas e o rascunho de resposta.

O orçamento `por_pessoa` é multiplicado pelo número de adultos para obter o limite total. O catálogo trabalha somente com BRL; o fluxo não converte outras moedas.

## Catálogo incluído

| ID | Origem → destino | Datas | Adultos | Valor total fictício |
| --- | --- | --- | ---: | ---: |
| `DEMO-01` | São Paulo → Buenos Aires | 15 a 19/11/2026 | 2 | R$ 5.200 |
| `DEMO-02` | São Paulo → Buenos Aires | 15 a 19/11/2026 | 2 | R$ 6.800 |
| `DEMO-03` | São Paulo → Santiago | 15 a 19/11/2026 | 2 | R$ 5.700 |

As três opções consideram **zero crianças**. Para mudar preços ou roteiros, edite o nó **Catálogo fictício - 3 opções** e teste novamente as regras.

## Preparar e testar no n8n

1. Importe o JSON. O workflow vem **desativado** e sem credenciais embutidas.
2. Abra **Conectar modelo de IA** e selecione sua credencial OpenAI ou Gateway credits, se disponíveis na sua instância. Confirme o modelo habilitado antes de testar.
3. Execute o workflow em modo de teste e envie: `Quero ir de São Paulo a Buenos Aires de 15/11/2026 a 19/11/2026. Serão 2 adultos, sem crianças. Meu orçamento total é de R$ 6.000.`
4. Confira os campos extraídos. Se a extração corresponder ao texto, apenas `DEMO-01` cabe no orçamento.
5. Faça novos testes omitindo “sem crianças”, o ano das datas ou a indicação de orçamento total/por pessoa. O resultado esperado é uma pergunta de esclarecimento, sem consulta conclusiva ao catálogo.

O formulário usa o caminho `fiat-agencia-com-ia-v2`, fuso `America/Sao_Paulo` e limite de execução de 120 segundos. A extração ao vivo depende do modelo e da credencial configurados e **precisa ser validada** na sua instância.

## Resultados, custos e privacidade

O resultado pode ser `precisa_complemento`, `opcao_encontrada` ou `sem_opcao_compativel`. O texto exibido é um **rascunho para revisão**. Nenhuma reserva, mensagem ou notificação a um atendente é criada por este workflow.

O texto do pedido é enviado ao provedor do modelo configurado, o que pode gerar custo de API. O workflow também salva execuções bem-sucedidas e com erro no histórico do n8n, incluindo dados do pedido. Use dados fictícios na demonstração e avalie a retenção do histórico antes de receber informações reais de clientes.
