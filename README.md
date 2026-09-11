# Investment Automation AI — n8n + RPA + IA Generativa

Evolução do laboratório **“Criando um Assistente de Investimentos com RPA e IA Generativa”**, originalmente proposto pela DIO.

O projeto mantém a ideia central do desafio — receber clientes, cruzar perfil com opções de investimento e gerar comunicação personalizada — e amplia o fluxo com **IA generativa, notícias via RSS e canais de comunicação**.

> **Aviso:** este projeto é educacional. As mensagens e dados não constituem recomendação financeira personalizada.

## Arquitetura do Fluxo

![Arquitetura do Fluxo de Investimentos](arquitetura_fluxo_investimentos_ia_2.png)

### O que foi melhorado (Versão Atualizada com AI Agent)

A arquitetura evoluiu de simples requisições HTTP para a utilização de **nós nativos do LangChain (AI Agent)** e integrações oficiais do n8n para otimizar o uso de memória e envio de mensagens:

```text
Cliente/RPA
   ↓
Webhook
   ├──────────────→ Catálogo de investimentos (CSV)
   │
   └──────────────→ Notícias RSS (BBC/G1/Valor)
                      ↓
              Normalização dos dados
                      ↓
             Motor de recomendação
                      ↓
                 Prompt IA + Contexto
                      ↓
          🤖 AI Agent (Google Gemini Chat Model) + Memory Buffer
                      ↓
          Mensagem personalizada gerada
             ┌────────┼────────┐
             ↓        ↓        ↓
      Nó Gmail     Nó Telegram   Webhook Response
