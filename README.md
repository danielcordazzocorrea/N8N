# 🚀 Meus Workflows n8n

Bem-vindo ao meu repositório de automações desenvolvidas no **n8n**. Aqui você encontrará soluções que variam de disparos em massa até assistentes de IA complexos com memória e integração oficial com a META.

---

## 📂 Estrutura do Repositório

O repositório está organizado em pastas independentes. Cada pasta contém o arquivo `.json` do workflow e um manual específico de configuração.

---

## 🛠️ Como Rodar as Automações

Para utilizar qualquer um dos workflows deste repositório, siga os passos abaixo:

### 1. Preparação do Ambiente
* Certifique-se de ter uma instância do **n8n** ativa (Desktop, Docker ou Cloud).
* Para o Assistente IA, você precisará de instâncias do **Redis** e **Supabase** (PostgreSQL).
* Para as automações de WhatsApp, é necessário uma conta no **Meta for Developers** com a WhatsApp Cloud API configurada.

### 2. Importação
1. Entre na pasta do projeto desejado.
2. Baixe o arquivo `.json` ou copie o seu conteúdo.
3. No seu n8n, clique em **Workflows** > **Import from File** (ou apenas cole o JSON na tela do editor).

### 3. Configuração de Credenciais
Os arquivos JSON deste repositório foram limpos por segurança. Ao importar, você precisará:
* Criar e selecionar suas próprias **Credentials** para OpenAI, Supabase, Redis, etc.
* Substituir os campos marcados com placeholders, como:
    * `{ YOUR_URL }`: Sua URL do Chatwoot ou API.
    * `{ YOUR_TOKEN }`: Tokens de acesso.
    * `{ PUT_SYSTEM_MESSAGE }`: Instruções de comportamento da IA.

---

## 🔐 Segurança e Boas Práticas
* **Sem Hardcoding:** Nenhum workflow neste repositório contém chaves de API ou senhas reais.
* **Modularidade:** Os fluxos são desenhados para serem escaláveis e fáceis de adaptar para diferentes modelos de negócio.
* **Lógica Anti-Erro:** Implementação de nós de tratamento de erro e controle de concorrência (Redis) para garantir estabilidade.

---

## 👨‍💻 Sobre Mim
Desenvolvedor focado em automação de processos e integração de sistemas de IA para eficiência operacional.
