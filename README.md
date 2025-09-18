# 🤖 Automação de Agendamento com n8n

<img width="1005" height="389" alt="n8n workflow" src="https://github.com/user-attachments/assets/bfe05166-3e42-4af3-a24c-30a7b78c80aa" />

Este repositório contém um **workflow do n8n** para automação de agendamento de consultas médicas com integração ao **Telegram** e **Google Calendar**.

## 📌 Funcionalidades
- Recebe mensagens de pacientes via **Telegram Bot**.
- Extrai informações como datas preferenciais e nome do paciente.
- Usa **IA (Gemini 2.5 Flash)** para interpretar pedidos e gerenciar o fluxo.
- Checa disponibilidade de horários no **Google Calendar**.
- Valida datas de exames (exame ≤ 6 meses).
- Cria eventos confirmados no **Google Calendar**.
- Responde ao paciente pelo Telegram com confirmações.

---

## ⚙️ Estrutura do Workflow N8N 
Sequencia de nós:  
1. Telegram Trigger: Inicia o fluxo ao receber uma mensagem 
2. Edit Fields: Normaliza a mensagem, extrai o chatId, messageText, messageId, userFirstName, timestamp e injeta a data atual (today) para dar contexto à IA.  
3. AI Agent: O cérebro do fluxo. Ele processa a mensagem, usa o prompt e as ferramentas do Google Calendar (Consultar e Agendar) diretamente integradas para decidir qual ação tomar. Essa integração é o ponto principal da sua solução, eliminando a necessidade de um nó If.
    - Chat model: gemini 2.5 flash
    - tools:  Consultar (Google Calendar -> get many) e Agendar (Google Calendar -> create)
4. Edit Fields - Este nó é o Regex para limpar a saída da IA, para garantir que a mensagem final seja enviada corretamente para o Telegram.
5. Telegram Send: Envia a resposta final para o paciente.
6. Atendimento finalizado (No operation, do nothing) -> Um nó de finalização que mantém o fluxo limpo e organizado.


## 🛠️ Pré-requisitos

1. **Conta no [n8n](https://n8n.io/)** (cloud ou self-hosted).
2. **Conta Google** com acesso ao Calendar.
3. **Bot no Telegram** (criado via [@BotFather](https://t.me/BotFather)).

---

## 📥 Importando o Workflow

1. Baixe o arquivo JSON do workflow em [`/workflows`](./workflows).
2. No n8n, vá em **Workflows > Import from File**.
3. Selecione o arquivo `.json`.
4. Salve o workflow.

---

## 🔑 Configuração de Credenciais

Após importar, você precisa configurar as credenciais:

### 1. Google Calendar
- No [Google Cloud Console](https://console.cloud.google.com/):
  - Crie um projeto.
  - Ative a **Google Calendar API**.
  - Vá em **APIs & Services > Credentials**.
  - Crie um **OAuth Client ID** (tipo Web Application).
  - Adicione como redirect URI:  
    ```
    https://SEU_N8N_URL/rest/oauth2-credential/callback
    ```
  - Copie o **Client ID** e **Client Secret**.
- No n8n:
  - Vá em **Credentials > Google API**.
  - Cole Client ID e Client Secret.
  - Faça login com sua conta Google e permita acesso ao Calendar.

### 2. Telegram
- Crie um bot com [@BotFather](https://t.me/BotFather).
- Use o comando `/newbot`.
- Dê um nome e username para o bot.
- Copie o **Bot Token**.
- No n8n:
  - Vá em **Credentials > Telegram API**.
  - Cole o Token.

---

## 🚀 Executando o Workflow

1. Ative o workflow no n8n.
2. Envie uma mensagem para o seu bot no Telegram.
3. O fluxo será:
   - Interpretar a mensagem com IA.
   - Checar horários no Google Calendar.
   - Validar exames, se necessário.
   - Confirmar ou sugerir horários.
   - Criar o evento no Google Calendar.
   - Responder ao paciente no Telegram.

---

## 📂 Estrutura do Repositório
```
clinica-agendamento-n8n/
├── workflows/
│ └── agendamento.json # Workflow exportado do n8n
└── README.md # Este arquivo
```
---
