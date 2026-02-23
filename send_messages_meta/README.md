# 🚀 Automação: Envio em Massa de WhatsApp (API Cloud) + Supabase

Este workflow do n8n foi desenvolvido para realizar disparos em massa de mensagens de template do WhatsApp utilizando a API oficial da Meta, integrando-se ao banco de dados Supabase para gestão de contatos e controle de etapas.

## 📋 Como o Fluxo Funciona
1.  **Trigger Manual:** O processo é iniciado manualmente.
2.  **Consulta Supabase:** Busca na tabela `dados_cliente` todos os registros onde a coluna `etapa` é igual a `0`.
3.  **Processamento em Lote:** O nó *Split Out* e o *Loop* organizam os números para processamento individual.
4.  **Controle de Status:** Antes do envio, o workflow atualiza a `etapa` do cliente para `1`, evitando envios duplicados em caso de reinicialização do fluxo.
5.  **Envio de Mensagem:** Dispara um template de WhatsApp (com suporte a imagem no cabeçalho) via requisição HTTP POST.
6.  **Delay de Segurança:** Um nó de *Wait* de 1 segundo é aplicado entre cada envio para respeitar limites de taxa e garantir estabilidade.

## 🛠️ Pré-requisitos
* **n8n** instalado e configurado.
* **Supabase:** Tabela `dados_cliente` com as colunas `telefone` e `etapa`.
* **Meta for Developers:** Aplicativo configurado com a API de WhatsApp Cloud ativa.

## ⚙️ Configuração dos Placeholders
Ao importar o JSON, você deve substituir os seguintes campos no nó **HTTP Request**:

| Campo | Onde encontrar |
| :--- | :--- |
| `{ your_id }` | No painel da Meta (Phone Number ID). |
| `{ YOUR_TOKEN }` | Seu Permanent Access Token da Meta. |
| `{ YOUR_MODEL }` | O nome exato do seu template aprovado no Gerenciador do WhatsApp. |
| `{ YOUR_ID }` | O ID da imagem (Media ID) hospedada na Meta para o cabeçalho. |

## 📦 Como Importar
1.  Crie um novo workflow no n8n.
2.  Copie o conteúdo do arquivo `workflow.json` deste repositório.
3.  Cole diretamente na tela do n8n.
4.  Configure suas credenciais do Supabase nos nós correspondentes.