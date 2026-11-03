# FIAT — Agência de viagens sem API

Este diretório contém o workflow n8n [`01_FIAT_Agencia_Sem_API.json`](01_FIAT_Agencia_Sem_API.json). Ele demonstra como receber um pedido de viagem por formulário, validar as informações e comparar o pedido com **três opções fictícias**. Toda a lógica roda em nós do próprio n8n: não há IA, consulta a uma agência, reserva ou envio de mensagem.

## O que entra no formulário

| Campo | Como é usado |
| --- | --- |
| `origem` e `destino` | Cidades comparadas com o catálogo. |
| `data_ida` e `data_volta` | Datas da viagem; a volta precisa ser posterior à ida. |
| `adultos` | Quantidade de adultos, entre 1 e 20. |
| `orcamento_valor` | Valor positivo em reais, informado como número. |
| `orcamento_tipo` | Indica se o valor é **Total da viagem** ou **Por pessoa**. |

Os campos não são obrigatórios no formulário de propósito: deixá-los vazios permite demonstrar como o fluxo identifica pendências. O nó **Organizar campos** acrescenta `moeda: BRL`, considera `criancas: 0` nesta versão e traduz as opções do orçamento para `total` ou `por_pessoa`.

## Como o fluxo funciona

1. **Receber dados:** abre o formulário do n8n.
2. **Organizar campos e Conferir os dados:** padroniza o pedido e valida cidades, datas completas, número de adultos, orçamento e tipo do orçamento. Datas podem ser interpretadas em `DD/MM/AAAA` ou `AAAA-MM-DD`; datas inexistentes são rejeitadas.
3. **Dados suficientes?:** quando há alguma pendência, prepara uma pergunta de complemento e encerra a consulta ao catálogo.
4. **Catálogo fictício - 3 opções:** carrega as opções de demonstração descritas abaixo.
5. **Comparar as condições:** exige correspondência de origem, destino, datas, viajantes e moeda, além de preço dentro do orçamento. O fluxo registra por que cada opção foi aceita ou rejeitada.
6. **Encontrou opção?:** prepara um rascunho com as opções compatíveis ou um rascunho para revisão humana quando nenhuma atende ao pedido.
7. **Resultado na página:** mostra os dados validados, a comparação feita e o texto preparado.

Se o orçamento for **por pessoa**, o fluxo multiplica o valor pelo número de adultos antes de comparar com o preço **total** do catálogo. A comparação das cidades ignora diferenças de maiúsculas e acentos e reconhece `SP` como São Paulo. As datas e a quantidade de viajantes precisam corresponder exatamente aos registros do catálogo.

## Catálogo incluído

Todos os valores e itinerários abaixo são fictícios. As três opções consideram **dois adultos, nenhuma criança e moeda BRL**.

| ID | Origem → destino | Ida → volta | Valor total |
| --- | --- | --- | ---: |
| `DEMO-01` | São Paulo → Buenos Aires | 15/11/2026 → 19/11/2026 | R$ 5.200 |
| `DEMO-02` | São Paulo → Buenos Aires | 15/11/2026 → 19/11/2026 | R$ 6.800 |
| `DEMO-03` | São Paulo → Santiago | 15/11/2026 → 19/11/2026 | R$ 5.700 |

Para alterar a demonstração, edite a lista no nó **Catálogo fictício - 3 opções**. Isso não transforma o workflow em consulta de disponibilidade real.

## Preparar e testar no n8n

1. Importe o arquivo JSON em uma instância do n8n. Ele vem **desativado** e não requer credenciais externas.
2. Execute o workflow em modo de teste e abra o formulário do nó **Receber dados**.
3. Informe `São Paulo` → `Buenos Aires`, ida em `15/11/2026`, volta em `19/11/2026`, `2` adultos, orçamento **total** de `6000`. O resultado esperado é apenas `DEMO-01`.
4. Repita sem informar as datas. O fluxo deve pedir as datas antes de consultar o catálogo.
5. Repita com orçamento total de `5000`. O fluxo deve indicar que não existe opção compatível no catálogo.

O caminho configurado para o formulário é `fiat-agencia-sem-api`. O workflow usa o fuso `America/Sao_Paulo` e limite de execução de 120 segundos.

## Resultados e limites

O resultado pode ter os estados `precisa_complemento`, `opcao_encontrada` ou `sem_opcao_compativel`. Em todos eles, a resposta é um **rascunho para revisão**. O nó chamado **Preparar encaminhamento** apenas escreve esse rascunho; ele não abre um chamado, avisa um atendente nem envia uma mensagem ao viajante.

Esta versão só representa viagens de adultos e um catálogo pequeno, fixo e fictício. Não use os valores como cotação. As execuções bem-sucedidas e com erro são salvas no histórico do n8n; use dados fictícios nos testes e revise a política de retenção antes de usar dados reais.
