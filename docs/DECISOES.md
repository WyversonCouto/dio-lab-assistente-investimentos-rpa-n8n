# 📋 Documentação de Decisões Técnicas & Evidências de Execução

Este documento formaliza as decisões de arquitetura e engenharia adotadas para o correto funcionamento do ecossistema de automação.

---

## 🛠️ Decisões Técnicas Adotadas

### 1. Uso do Ngrok para Comunicação Hybrid-Cloud
- **Motivação**: O script Python de extração de clientes executa em ambiente cloud (Google Colab), enquanto a instância do n8n roda localmente via Docker.
- **Solução**: O Ngrok foi configurado para expor a porta `5678` do n8n local por meio de um túnel HTTP seguro (`https://...ngrok-free.dev`), permitindo ao Colab realizar o disparo das requisições POST para o Webhook sem falhas de roteamento local (`Connection Refused`).

### 2. Substituição do Modelo OpenAI pelo Provedor Groq Cloud API
- **Motivação**: Contornar o erro de cota zerada (*Zero-quota error / 429*) na API paga da OpenAI sem comprometer a capacidade de geração de linguagem natural.
- **Solução**: O nó de integração no n8n foi direcionado para os servidores da **Groq API** (`https://api.groq.com/openai/v1`) utilizando o modelo ultra-rápido `GPT-OSS-20B` / `Llama 3`. A propriedade `Store` foi configurada para `false` para garantir a compatibilidade do protocolo.

### 3. Redirecionamento de Testes via Nó `Edit Fields`
- **Motivação**: Garantir a validação fim a fim do disparo pelo Gmail sem enviar mensagens reais para os endereços fictícios do CSV de teste (`ana@email.com`, `bruno@email.com`, etc.).
- **Solução**: O nó `Edit Fields` foi inserido antes da etapa de envio no Gmail, realizando o *override* dinâmico do parâmetro `to` para a caixa de e-mail do próprio desenvolvedor, garantindo a chegada das 10 recomendações de teste formatadas em HTML.

---

## ✅ Evidências do Fluxo de Execução

O fluxo foi validado e executado com sucesso em todas as etapas:

1. **Extração (RPA / Colab)**:
   - Status: HTTP 200 OK enviado para a URL do Webhook do Ngrok.
   - 10 clientes extraídos e transmitidos com sucesso.

2. **Orquestração & Processamento (n8n)**:
   - Regras de compatibilidade entre saldo, perfil e investimento processadas via JavaScript.
   - Geração e parsing do JSON de e-mail pelo modelo de IA.

3. **Disparo e Envio (Gmail)**:
   - 10 mensagens disparadas com confirmação do servidor (`labelIds: ["SENT"]`).
   - Recebimento confirmado na caixa de entrada real.
