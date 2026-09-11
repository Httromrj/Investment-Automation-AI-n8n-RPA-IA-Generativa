# Investment Automation AI — n8n + RPA + IA Generativa

Evolução do laboratório **“Criando um Assistente de Investimentos com RPA e IA Generativa”**, originalmente proposto pela DIO.

O projeto mantém a ideia central do desafio — receber clientes, cruzar perfil com opções de investimento e gerar comunicação personalizada — e amplia o fluxo com **IA generativa, notícias via RSS e canais de comunicação**.

> **Aviso:** este projeto é educacional. As mensagens e dados não constituem recomendação financeira personalizada.

## O que foi melhorado

### Versão original

```text
Cliente/RPA
   ↓
Webhook n8n
   ↓
CSV de investimentos
   ↓
Regra por perfil/saldo
   ↓
Mensagem estática
   ↓
JSON
```

### Versão atual

```text
Cliente/RPA
   ↓
Webhook
   ├──────────────→ Catálogo de investimentos
   │
   └──────────────→ Notícias RSS
                      ↓
              Normalização dos dados
                      ↓
             Motor de recomendação
                      ↓
                 Prompt IA
                      ↓
                 Gemini API
                      ↓
          Mensagem personalizada
             ┌────────┼────────┐
             ↓        ↓        ↓
          E-mail   Telegram   Webhook
```

## Funcionalidades

- Recepção de clientes por Webhook.
- Leitura do catálogo de investimentos hospedado no GitHub Pages da DIO.
- Normalização do CSV.
- Motor determinístico de recomendação por perfil e saldo.
- Coleta de notícias por RSS.
- Geração de mensagem com Gemini.
- Proteção contra invenção de dados: a IA recebe somente dados estruturados pelo workflow.
- Envio opcional por e-mail.
- Envio opcional pelo Telegram.
- Resposta JSON para integração com outros sistemas.
- Variáveis de ambiente para evitar colocar tokens diretamente no workflow.

## Estrutura

```text
dio-lab-assistente-investimentos-rpa-n8n/
├── README.md
├── n8n/
│   └── workflow-investment-ai-v2.json
├── src/
│   └── extrair_clientes.ipynb
└── docs/
    ├── index.html
    └── data.csv
```

## Importação no n8n

1. Abra o n8n.
2. Acesse **Workflows**.
3. Importe `n8n/workflow-investment-ai-v2.json`.
4. Configure as variáveis de ambiente.
5. Configure a credencial SMTP.
6. Execute primeiro em modo de teste.
7. Só depois habilite os canais de envio.

## Variáveis de ambiente

### Gemini

```env
GEMINI_API_KEY=sua_chave
```

### Telegram

O envio fica desabilitado por padrão.

Para habilitar:

```env
ENVIAR_TELEGRAM=true
TELEGRAM_BOT_TOKEN=seu_token
```

O cliente precisa possuir `telegram_chat_id`.

### E-mail

Configure uma credencial SMTP no n8n e, opcionalmente:

```env
SMTP_FROM_EMAIL=seu-email@dominio.com
```

**Não coloque tokens, senhas ou chaves no JSON versionado no GitHub.**

## Payload do Webhook

O workflow espera um POST semelhante a:

```json
{
  "clientes": [
    {
      "nome": "Ana Silva",
      "email": "ana@example.com",
      "saldo": "R$ 12.500,00",
      "perfil": "Conservador",
      "telegram_chat_id": ""
    }
  ]
}
```

Perfis suportados pelo catálogo atual:

- Conservador
- Moderado
- Arrojado

## IA generativa

A IA não decide o produto de investimento.

O fluxo primeiro executa a regra determinística:

```text
perfil + saldo + catálogo
        ↓
opção compatível
        ↓
IA
        ↓
comunicação
```

Isso separa **regra de negócio** de **geração de linguagem**, reduzindo o risco de a LLM alterar valores, rentabilidades ou critérios do catálogo.

## Notícias

A versão atual demonstra ingestão de notícias através de RSS.

O RSS é convertido para uma estrutura simples:

```json
{
  "titulo": "Título da notícia",
  "link": "https://...",
  "data": "..."
}
```

Essa camada foi criada para permitir a substituição futura por outras fontes, como APIs financeiras, RSS adicionais ou fontes oficiais.

## Canais

### Webhook

Sempre retorna os resultados processados em JSON.

### E-mail

Pode ser ativado configurando uma credencial SMTP no n8n.

### Telegram

Pode ser ativado através de `ENVIAR_TELEGRAM=true`.

O envio está separado da geração da mensagem para que o workflow possa ser testado sem disparar mensagens reais.

## Segurança

Este repositório não deve conter:

- API keys;
- tokens de bots;
- senhas SMTP;
- credenciais do n8n;
- dados reais de clientes;
- identificadores pessoais desnecessários.

Use variáveis de ambiente e credenciais gerenciadas pelo n8n.

## Próximas evoluções

- Persistência em PostgreSQL.
- Histórico de mensagens enviadas.
- Deduplicação de notícias.
- Controle de opt-in/opt-out.
- Retry e tratamento de falhas de APIs.
- Mais fontes de notícias.
- Dashboard de execução.
- WhatsApp via API.
- Classificação automática de notícias.
- Agendamento periódico.
- Testes automatizados dos dados de entrada.

## Tecnologias

- Python / BeautifulSoup — RPA
- n8n — orquestração
- Gemini — IA generativa
- HTTP / REST APIs
- RSS
- GitHub Pages
- SMTP
- Telegram Bot API

## Origem

Projeto baseado no laboratório da DIO:

**Criando um Assistente de Investimentos com RPA e IA Generativa**

O objetivo desta versão é demonstrar a evolução do laboratório para uma arquitetura de automação mais próxima de um cenário de integração real.

## Autor

**Rômulo da Silva de Oliveira**

GitHub: https://github.com/Httromrj

LinkedIn: https://www.linkedin.com/in/romhome/
