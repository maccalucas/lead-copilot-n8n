# Lead Copilot — Pré-qualificação de Leads com IA

Copiloto de pré-qualificação de leads B2B usando LLM para analisar perfil de empresas e gerar score de fit automaticamente.

## Problema

Equipes comerciais B2B perdem 15–30 minutos qualificando manualmente cada lead — pesquisando a empresa, avaliando fit com o ICP e decidindo a abordagem. Para times enxutos com alto volume de leads, isso é um gargalo direto de receita.

**Público-alvo:** PMEs com time de vendas enxuto e fluxo de leads inbound ativo.

## Solução

Workflow automatizado que recebe os dados do lead, enriquece com informações públicas da empresa via Google Places, analisa o conteúdo do site e gera um score de 0–10 com justificativa e sugestão de abordagem — tudo em menos de 10 segundos.

## Fluxo
Form Trigger → Edit Fields → Google Places (Text Search) → Google Places (Details)
→ HTTP GET Site → AI Agent (LLM) → Code (JSON parse) → IF score ≥ 7
→ Gmail lead quente / Gmail lead frio





## Stack

- **Orquestração:** n8n (self-hosted)
- **LLM:** OpenAI GPT-5.4 via AI Agent node
- **Integrações externas:** Google Places API, Gmail
- **Interface:** n8n Form (substituível por webhook WhatsApp)

## Como executar

### Pré-requisitos

- n8n instalado (self-hosted ou cloud)
- Conta Google Cloud com Places API habilitada
- Gmail conectado no n8n

### Configuração

1. Clone o repositório
2. Copie `.env.example` para `.env` e preencha as variáveis
3. No n8n: importe o arquivo `workflow.json`
4. Configure as credenciais:
   - `Google Places API Key` no node HTTP Request
   - `OpenAI API Key` no node OpenAI Chat Model
   - `Gmail OAuth2` no node Gmail
5. Ative o workflow e acesse o form via URL gerada pelo trigger

## KPIs de Sucesso

**Técnicos**
- Latência end-to-end < 10s
- Taxa de erro de parse do JSON do LLM < 2%
- Taxa de enriquecimento bem-sucedido via Places API > 95%

**Negócio**
- Redução no tempo médio de qualificação por lead
- Taxa de acerto do score validada com o time de vendas após 1 semana
- % de leads quentes que avançaram no pipeline após notificação automática

## Plano de Validação

**Hipótese:** o score do LLM concorda com o julgamento humano do SDR em ≥ 70% dos casos.

**Execução em 1 semana:**
1. Pegar os últimos 30 leads com desfecho conhecido
2. Rodar o workflow nos dados históricos
3. Comparar score gerado com decisão real do SDR
4. Calcular acurácia e refinar o system prompt se necessário

## Decisões Técnicas e Trade-offs

| Decisão | Motivo | Trade-off |
|---|---|---|
| n8n como orquestrador | Iteração rápida, inspeção visual de execuções, sem código extra | Sem suporte nativo a RAG ou memória vetorial |
| Google Places API | Dados de presença digital e reputação sem necessidade de CNPJ | Depende do nome da empresa estar cadastrado corretamente |
| Form Trigger (substituível por webhook) | Velocidade de POC | Versão produtiva deve usar webhook WhatsApp para aderência ao canal |
| Temperature 0.1 no LLM | JSON consistente e parseable | Menos criatividade na sugestão de abordagem |

## Evolução prevista

- Trocar Form Trigger por webhook WhatsApp (Blip / Z-API)
- Adicionar Brasil API (CNPJ) como segunda fonte de enriquecimento
- Persistir leads no Supabase para histórico e análise
- Dashboard de scores por período
