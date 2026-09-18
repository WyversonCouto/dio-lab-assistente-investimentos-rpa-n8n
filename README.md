# 🚀 Assistente de Investimentos RPA + n8n

> **Solução de Automação de Investimentos**: Integração de Web Scraping, orquestração no n8n, geração de e-mails via IA (Groq) e envio automatizado via Gmail.

---

## 📌 Fluxo da Arquitetura

1. **🌐 Extração de Dados (RPA)**
   - Script Python no Google Colab realiza Web Scraping da tabela HTML de clientes.
   - Envia os dados extraídos via Webhook.

2. **🔗 Conectividade (Túnel Ngrok)**
   - Túnel HTTP seguro conectando o Google Colab em nuvem ao n8n no ambiente Docker local.

3. **⚡ Processamento & Regras (n8n)**
   - **HTTP Request**: Busca a base de dados em CSV dos produtos de investimento.
   - **Code Node**: Realiza o filtro de adequação cruzando perfil, saldo do cliente e rentabilidade.

4. **🧠 Inteligência Artificial (Groq API)**
   - Nó do modelo LLM (`GPT-OSS-20B`) gera a proposta de e-mail personalizada em formato JSON.
   - **Code Node**: Formata a resposta gerando o corpo do e-mail estruturado em HTML.

5. **📧 Validação & Disparo (Gmail)**
   - **If Node**: Valida o formato do e-mail.
   - **Edit Fields**: Redireciona o campo `to` para a caixa de testes real.
   - **Gmail Node**: Efetua o envio real da mensagem.

---

## 🛠️ Tecnologias Utilizadas

| Componente | Tecnologia | Função |
| :--- | :--- | :--- |
| **Extração** | Python (Colab) | Raspagem de dados da tabela HTML de clientes. |
| **Túnel** | Ngrok | Conexão entre o Google Colab e o container n8n local. |
| **Orquestração** | n8n (Docker) | Pipeline de regras de negócio e integrações. |
| **Inteligência Artificial** | Groq API | Geração personalizada das recomendações de e-mail. |
| **Envio** | Gmail API | Validação e disparo dos e-mails formatados. |

---

## 💡 Decisões Técnicas e Soluções de Desafios

1. **Conexão Colab com Docker Local (Ngrok)**
   - O Google Colab roda na nuvem e não acede a `localhost:5678`. O **Ngrok** foi utilizado para expor com segurança o endpoint do n8n local.

2. **Substituição da API OpenAI pela Groq**
   - Para contornar limites de cota da OpenAI, utilizamos a **Groq Cloud API** com a *Custom Base URL* (`[https://api.groq.com/openai/v1](https://api.groq.com/openai/v1)`) e a opção `Store` desativada (`false`).

3. **Sobrescrita de E-mail para Testes (`Edit Fields`)**
   - Para testar o disparo sem enviar e-mails para endereços fictícios do CSV, o nó **Edit Fields** substitui dinamicamente o campo `to` pelo e-mail real de validação.

---

## 📁 Estrutura do Repositório

```text
.
├── n8n/
│   └── workflow.json          # Workflow completo do n8n
├── rpa/
│   └── extrair_clientes.ipynb # Código Python de Web Scraping
├── docs/
│   ├── data.csv               # Dados dos ativos
│   └── index.html             # Página HTML do projeto
└── README.md                  # Documentação principal
