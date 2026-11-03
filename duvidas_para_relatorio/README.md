# FIAT — Dúvidas da turma para relatório

Este diretório contém o workflow n8n [`03_FIAT_Duvidas_Para_Relatorio.json`](03_FIAT_Duvidas_Para_Relatorio.json). Ele transforma uma lista de dúvidas da turma em um relatório agrupado por assunto e sugere uma pauta de revisão para o professor. O agrupamento é feito por regras locais: **não usa IA**, não classifica automaticamente o conteúdo e não avalia a aprendizagem dos alunos.

## Formato de entrada

O formulário **Receber dúvidas** tem um campo obrigatório, `duvidas`. Informe **uma dúvida por linha**, separando o assunto e a pergunta com o primeiro caractere `|`:

```text
Frações | Como somar denominadores diferentes?
Frações | Quando posso simplificar?
Equações | Como isolar a incógnita?
```

O assunto deve vir preenchido em cada linha. Ele não é inferido a partir da pergunta. Espaços no começo e no fim de cada parte são removidos, e linhas em branco são ignoradas.

## Processamento

1. **Receber dúvidas:** recebe o texto colado no formulário.
2. **Organizar e agrupar dúvidas:** separa as linhas, identifica o assunto antes de `|` e a dúvida depois dele, e registra linhas sem assunto, sem separador ou sem pergunta como inválidas.
3. Assuntos escritos com diferenças de maiúsculas ou acentos são reunidos no mesmo grupo. A grafia exibida no relatório é a da **primeira ocorrência**. As perguntas são mantidas na ordem em que foram recebidas e não são deduplicadas.
4. Os grupos são ordenados pela **quantidade de dúvidas**, da maior para a menor; empates são ordenados alfabeticamente pelo nome do assunto.
5. **Mostrar relatório:** apresenta contagens, grupos, dúvidas, linhas a corrigir e uma pauta genérica de revisão para cada assunto.

As posições indicadas para linhas inválidas consideram as **linhas não vazias** recebidas. Se houver linhas inválidas, as válidas ainda aparecem e o resultado é marcado como **parcial**.

## Saída esperada

Com o exemplo acima, o relatório mostra **3 dúvidas em 2 assuntos**: Frações com duas perguntas e Equações com uma. Para cada grupo, a pauta sugerida pede que o professor revise o assunto e escolha um exemplo para discutir as dúvidas.

Se acrescentar `Não entendi a aula` como outra linha, ela será marcada como inválida porque não contém `assunto | dúvida`. O relatório continuará com as três perguntas válidas e informará qual linha precisa ser corrigida.

A contagem mede apenas as dúvidas recebidas. Ela **não** mede dificuldade, domínio do conteúdo nem a quantidade de alunos que fizeram perguntas; uma mesma pessoa pode enviar várias linhas.

## Preparar e testar no n8n

1. Importe o arquivo JSON em uma instância do n8n. O workflow vem **desativado** e não precisa de credenciais externas.
2. Execute o workflow em modo de teste e cole o exemplo da seção **Formato de entrada** no formulário **Receber dúvidas**.
3. Confira as contagens e a ordem dos grupos no relatório exibido na página.
4. Adicione uma linha sem `|` para verificar o aviso de relatório parcial. Teste também `Fracoes` e `FRAÇÕES` para confirmar que são agrupados com `Frações`.

O formulário usa o caminho `fiat-duvidas-relatorio`, fuso `America/Sao_Paulo` e limite de execução de 120 segundos. O histórico do n8n salva execuções bem-sucedidas e com erro; se usar dúvidas reais, evite nomes ou outros dados pessoais na entrada e reveja a retenção desse histórico.
