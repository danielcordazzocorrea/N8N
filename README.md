# N8N
# 📊 n8n Workflow: Gmail Pix to Supabase Dashboard

Este workflow automatiza a extração de dados de transações **Pix** (recebidos e enviados) diretamente da sua caixa de entrada do Gmail e os armazena em um banco de dados **Supabase**. Ideal para criar dashboards financeiros em tempo real.

## 🚀 Funcionalidades

- **Monitoramento em Tempo Real**: O gatilho verifica novos e-mails a cada minuto.
- **Filtro Inteligente (Switch)**: Identifica automaticamente se o e-mail é de um "Pix recebido" ou "Pix realizado".
- **Extração via Regex**: Utiliza JavaScript para extrair:
  - Valor da transação (R$).
  - Nome do remetente/destinatário.
  - Data e hora formatadas para o padrão ISO.
- **Persistência de Dados**: Salva tudo em uma tabela no Supabase.
-**Modelo de E-mail**: Automação configurada conforme o modelo de e-mail mandado pelo banco XP.
## 🛠️ Pré-requisitos

Para rodar este fluxo, você precisará de:
1. Uma instância do [n8n](https://n8n.io) instalada.
2. Credenciais de [Gmail OAuth2](https://docs.n8n.io) configuradas no Google Cloud Console.
3. Um projeto no [Supabase](https://supabase.com) com uma tabela chamada `pix_transactions`.
4. Uma conta em um banco que mande e-mal por pix enviado/recebido
5. Mude o workflow conforme o modelo de e-mail que o seu banco mandar

### Estrutura da Tabela no Supabase
Certifique-se de que sua tabela `pix_transactions` possua as seguintes colunas:
- `type` (text): "sent" ou "received".
- `amount` (numeric/float).
- `sender_name` (text).
- `receiver_name` (text).
- `transaction_date` (timestamp).

## 📥 Como Instalar

1. Baixe o arquivo `workflow.json` deste repositório.
2. No seu n8n, clique em **Workflows** > **Import from File**.
3. Selecione o arquivo baixado.
4. Configure suas credenciais do Gmail e do Supabase nos nós correspondentes.
5. No nó de inserção do Supabase, substitua o campo `[Coloque seu nome]` pelo seu nome real para identificar suas transaçõese mude .

## ⚠️ Segurança e Privacidade

Este JSON foi sanitizado. **Nenhuma credencial, senha ou token pessoal está incluído**. Certifique-se de configurar suas próprias chaves de API após a importação. Os nomes pessoais foram substituídos por placeholders.

---
Desenvolvido para automatizar o controle financeiro pessoal. 💸

## ATENÇÃO

Essa automação não é a sua versão final, ou seja, haverá melhorias