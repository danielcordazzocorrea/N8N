# 🤖 Assistente de IA Omnichannel de Elite (RAG + Redis + Whisper)

Este repositório contém um workflow avançado do **n8n** projetado para transformar o **Chatwoot (instância auto-hospedada)** em uma central de atendimento inteligente e autônoma, utilizando a **API oficial da META (WhatsApp Cloud API)**. 

O sistema resolve desafios complexos de automação, como o processamento de mensagens duplicadas, transcrição de áudio e a manutenção de contexto em conversas de longa duração.

---

## 🌟 Diferenciais Técnicos

### 1. Integração Profissional (Chatwoot + META)
Diferente de soluções baseadas em APIs não oficiais, este workflow utiliza a infraestrutura robusta do **Chatwoot auto-hospedado** conectado diretamente à **API oficial da META**. Isso garante maior estabilidade, segurança dos dados e conformidade com as políticas do WhatsApp.

### 2. Gestão de Concorrência com Redis (Anti-Loop)
Utiliza o **Redis** para criar uma trava de processamento (locking system).
* **Problema:** Usuários enviando várias mensagens seguidas podem confundir a IA.
* **Solução:** O workflow verifica no Redis se já existe um processamento ativo para aquele número. Se houver, ele encerra a execução atual, garantindo que apenas uma resposta seja gerada por vez.

### 3. Memória Persistente de Longo Prazo
Utiliza **PostgreSQL (Supabase)** para armazenar o histórico de conversas. Isso permite que a IA recorde interações passadas, oferecendo um atendimento personalizado mesmo após semanas do último contato.

### 4. Recuperação de Documentos (RAG)
Equipado com **Supabase Vector Store**, o assistente realiza buscas em bases de conhecimento (PDFs, manuais, catálogos) antes de responder, garantindo que as informações sejam precisas e evitando "alucinações" da IA.

### 5. Processamento de Áudio (Speech-to-Text)
Integração com **OpenAI Whisper** para transcrever mensagens de voz automaticamente. A IA entende e responde áudios como se fossem mensagens de texto.

---

## 🛠️ Tecnologias Utilizadas

* **Orquestrador:** n8n
* **Interface de Chat:** Chatwoot (Self-hosted / Auto-hospedado)
* **Canal de Comunicação:** API Oficial da META (WhatsApp Cloud API)
* **Inteligência Artificial:** OpenAI (GPT-4o / GPT-3.5)
* **Transcrição:** OpenAI Whisper
* **Banco de Dados:** PostgreSQL (Supabase)
* **Busca Vetorial:** pgvector (Supabase)
* **Cache e Fila:** Redis

---

## 📋 Estrutura do Banco de Dados (SQL)

Execute os comandos abaixo no editor SQL do seu Supabase para preparar o ambiente:

```sql
-- Tabela de Clientes
CREATE TABLE dados_cliente (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    telefone TEXT UNIQUE,
    nome TEXT,
    etapa TEXT DEFAULT '0',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tabela de Histórico (Memória da IA)
CREATE TABLE historico_chat_assistente (
    id SERIAL PRIMARY KEY,
    session_id TEXT, -- ID da conversa no Chatwoot
    message TEXT,
    role TEXT, -- 'user' ou 'assistant'
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);