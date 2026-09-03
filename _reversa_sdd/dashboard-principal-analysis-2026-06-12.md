# Analise Reversa - Dashboard Principal

Data da nova analise: 2026-06-12

Escala de confianca:
- 🟢 CONFIRMADO - extraido diretamente do codigo
- 🟡 INFERIDO - baseado em padroes observados
- 🔴 LACUNA - requer validacao humana

## 1. Identificacao da tela

| Item | Evidencia | Confianca |
|---|---|---|
| Rota canonica | `/dashboard` redireciona para `/profissional/dashboard` em `sst-finder/src/app/(app)/dashboard/page.tsx:4` | 🟢 CONFIRMADO |
| Pagina Next.js | `sst-finder/src/app/(app)/profissional/dashboard/page.tsx` renderiza `DashboardPrincipal` | 🟢 CONFIRMADO |
| Componente principal | `sst-finder/src/components/dashboard/dashboard-principal.tsx:128` | 🟢 CONFIRMADO |
| API consumida | `fetch('/api/profissional/dashboard')` em `dashboard-principal.tsx:137` | 🟢 CONFIRMADO |
| Endpoint de dados | `GET /api/profissional/dashboard` em `sst-finder/src/app/api/profissional/dashboard/route.ts:112` | 🟢 CONFIRMADO |

Conclusao: o dashboard principal atual e uma visao operacional do profissional autenticado. A rota generica `/dashboard` nao possui conteudo proprio; ela atua como atalho para o dashboard profissional.

## 2. Proposito de negocio

O dashboard consolida negocios, propostas, ordens de servico, pendencias, agenda, indicadores SST, indicadores ambientais e documentos em uma unica tela.

Objetivos observados:
- Dar uma visao rapida do funil comercial.
- Mostrar execucao operacional por status de OS.
- Destacar alertas com prazo vencido ou pendencias externas.
- Guiar proximas acoes do profissional.
- Separar blocos de dominio SST e Meio Ambiente.
- Oferecer acesso ao dashboard executivo por link direto.

## 3. Arquitetura funcional

| Camada | Responsabilidade | Evidencia | Confianca |
|---|---|---|---|
| Middleware | Redireciona raiz do app para `/dashboard` quando logado e bloqueia candidatos nas rotas da plataforma principal | `sst-finder/src/middleware.ts:56`, `sst-finder/src/middleware.ts:68` | 🟢 CONFIRMADO |
| Rota ponte | `/dashboard` redireciona para `/profissional/dashboard` | `sst-finder/src/app/(app)/dashboard/page.tsx:4` | 🟢 CONFIRMADO |
| Pagina | Obtem nome do usuario via NextAuth e injeta em `DashboardPrincipal` | `sst-finder/src/app/(app)/profissional/dashboard/page.tsx:6-11` | 🟢 CONFIRMADO |
| Componente UI | Controla estados de loading, erro e renderizacao dos blocos | `dashboard-principal.tsx:128-389` | 🟢 CONFIRMADO |
| API | Busca dados agregados no Prisma e retorna JSON consolidado | `route.ts:137-330` | 🟢 CONFIRMADO |
| Banco | Usa entidades Prisma de comercial, propostas, agenda, documentos, SST e ambiental | `sst-finder/prisma/schema.prisma` | 🟢 CONFIRMADO |

## 4. Contrato de dados da API

Formato retornado por `GET /api/profissional/dashboard`:

```ts
{
  kpis: {
    openBusiness: number
    proposalPipeline: number
    osInExecution: number
    awaitingClient: number
    completedThisMonth: number
    pipelineValue: number
  }
  commercial: { stages: { stage: string; count: number }[] }
  operational: { stages: { stage: string; count: number }[] }
  alerts: { label: string; severity: string; href: string }[]
  actions: { label: string; detail: string | null; href: string }[]
  sst: {
    openOs: number
    riscos: number
    inspecoes: number
    avaliacoes: number
    planosAbertos: number
  }
  environmental: {
    openOs: number
    licencas: number
    residuos: number
    condicionantes: number
  }
  documents: { total: number }
}
```

## 5. Fontes de dados

| Fonte Prisma | Uso no dashboard | Evidencia | Confianca |
|---|---|---|---|
| `serviceRequest` | Funil comercial, OS operacionais, pipeline financeiro, classificacao SST/ambiental | `route.ts:138-157`, `route.ts:197-218`, `route.ts:236-239` | 🟢 CONFIRMADO |
| `proposal` | Propostas em pipeline, vencidas e vencendo hoje | `route.ts:158-169`, `route.ts:209`, `route.ts:220-233` | 🟢 CONFIRMADO |
| `agendamento` | Proximas acoes de agenda nos proximos 7 dias | `route.ts:170-179`, `route.ts:270-276` | 🟢 CONFIRMADO |
| `documentoProfissional` | Total de documentos cadastrados | `route.ts:180`, `route.ts:326-328` | 🟢 CONFIRMADO |
| `planoAcao` | Planos abertos e atrasados | `route.ts:181-194`, `route.ts:262-266` | 🟢 CONFIRMADO |
| `riscoOcupacional`, `inspecao`, `avaliacaoQuantitativa` | KPIs SST | `route.ts:195-196`, `route.ts:314-320` | 🟢 CONFIRMADO |
| `licencaAmbiental`, `residuoManifesto` | KPIs ambientais | `route.ts:196`, `route.ts:322-325` | 🟢 CONFIRMADO |

## 6. Regras de negocio extraidas

| Regra | Descricao | Evidencia | Confianca |
|---|---|---|---|
| Autenticacao obrigatoria | Sem `session.user.id`, a API retorna 401 | `route.ts:112-116` | 🟢 CONFIRMADO |
| Escopo por profissional | Todas as buscas usam `userId` ou `professionalId` do usuario logado | `route.ts:118`, `route.ts:139`, `route.ts:171`, `route.ts:180-196` | 🟢 CONFIRMADO |
| Negocios em aberto | Conta `ServiceRequest` cujo `status` normalizado nao esta em `ganho` ou `perdido` | `route.ts:9`, `route.ts:208` | 🟢 CONFIRMADO |
| Pipeline de propostas | Conta propostas com status `enviada` ou `aceita` | `route.ts:209` | 🟢 CONFIRMADO |
| OS operacional | Um `ServiceRequest` vira OS quando possui `osNumber` | `route.ts:202` | 🟢 CONFIRMADO |
| Status operacional | Status vem de `osChecklist.operacionalStatus`; se ausente, assume `OS Criada` | `route.ts:86-88` | 🟢 CONFIRMADO |
| Concluidas no mes | Conta OS com status operacional `Concluido` e `updatedAt >= startOfMonth()` | `route.ts:212-215` | 🟢 CONFIRMADO |
| Valor de pipeline | Soma `ServiceRequest.valor` apos parser monetario tolerante a formato BR | `route.ts:78-82`, `route.ts:216-218` | 🟢 CONFIRMADO |
| Alertas criticos | OS vencida, proposta vencida e plano de acao atrasado entram como `critical` | `route.ts:248-266` | 🟢 CONFIRMADO |
| Alertas de atencao | OS aguardando cliente e proposta vencendo hoje entram como `warning` | `route.ts:243-247`, `route.ts:267-268` | 🟢 CONFIRMADO |
| Classificacao SST | Texto de servicos/checklist e comparado com termos SST hardcoded | `route.ts:10-25`, `route.ts:236` | 🟢 CONFIRMADO |
| Classificacao ambiental | Texto de servicos/checklist e comparado com termos ambientais hardcoded | `route.ts:27`, `route.ts:237` | 🟢 CONFIRMADO |

## 7. Componentes visuais e navegacao

| Area | Conteudo | Destinos principais | Confianca |
|---|---|---|---|
| Header | Saudacao, titulo `Dashboard Principal`, subtitulo e botao `Ver executivo` | `/profissional/dashboard/executivo` | 🟢 CONFIRMADO |
| KPIs | Negocios em aberto, propostas, OS em execucao, aguardando cliente, concluidas no mes | `/profissional/servicos`, `/profissional/propostas`, `/profissional/atividades` | 🟢 CONFIRMADO |
| Funil Comercial | Barras proporcionais por etapa comercial | `/profissional/servicos` | 🟢 CONFIRMADO |
| Operacao / Servicos | Lista de status operacionais com barras | `/profissional/atividades` | 🟢 CONFIRMADO |
| Alertas Importantes | Lista de alertas `critical` ou `warning` | Depende do alerta | 🟢 CONFIRMADO |
| Proximas Acoes | Agenda, pendencias de cliente e propostas enviadas | Agendamentos, atividades, propostas | 🟢 CONFIRMADO |
| SST | OS abertas, riscos, inspecoes, avaliacoes, planos abertos | Rotas SST especificas | 🟢 CONFIRMADO |
| Meio Ambiente | OS abertas, licencas, residuos, condicionantes | `/profissional/meio-ambiente` | 🟢 CONFIRMADO |

## 8. Estados da tela

| Estado | Comportamento | Evidencia | Confianca |
|---|---|---|---|
| Loading | Exibe spinner e texto `Carregando dashboard principal...` | `dashboard-principal.tsx:158-166` | 🟢 CONFIRMADO |
| Erro | Exibe card vermelho com mensagem de indisponibilidade | `dashboard-principal.tsx:170-178` | 🟢 CONFIRMADO |
| Vazio parcial | Alertas e acoes possuem mensagens vazias especificas | `dashboard-principal.tsx:294-323`, `dashboard-principal.tsx:327-342` | 🟢 CONFIRMADO |
| Sucesso | Renderiza KPIs, funis, alertas, acoes, SST e ambiental | `dashboard-principal.tsx:181-389` | 🟢 CONFIRMADO |

## 9. Algoritmos relevantes

### 9.1 Normalizacao textual

`normalize()` coloca texto em minusculas e remove acentos para comparacoes tolerantes.

Impacto:
- Permite comparar `Concluido` e `Concluido` com variacoes de acento.
- Tambem e usado para matching de termos SST/ambientais.

Risco:
- Matching por substring pode classificar falso positivo se o texto contiver termo em contexto nao relacionado.

### 9.2 Parser monetario

`parseMoney()` remove simbolos, trata separador de milhar e troca virgula por ponto.

Impacto:
- Permite somar valores armazenados como texto em `ServiceRequest.valor`.

Risco:
- Valores ambiguos ou formatos internacionais podem ser convertidos incorretamente.
- Como o campo e `String?` no Prisma, nao ha garantia forte de moeda, escala ou validade numerica.

### 9.3 Barras proporcionais

`progressWidth(count, max)` retorna no minimo `6%` quando ha algum maximo positivo, evitando barra invisivel.

Impacto:
- Melhora leitura visual mesmo com numeros pequenos.

Risco:
- Valores baixos podem parecer mais expressivos do que sao.

### 9.4 Agregacao paralela

A API usa `Promise.all` para buscar todas as fontes em paralelo.

Impacto:
- Reduz latencia geral do dashboard.

Risco:
- Qualquer falha em uma consulta derruba o endpoint inteiro e retorna 500.

## 10. Divergencia com testes existentes

Os testes E2E em `sst-finder/tests/e2e/professional-dashboard.spec.ts` parecem desatualizados em relacao ao dashboard atual.

Evidencias:
- Teste espera `Dashboard Profissional`, mas a tela atual renderiza `Dashboard Principal`.
- Teste espera cards `EM ANDAMENTO`, `AGENDADOS`, `CONCLUIDOS`, `FATURAMENTO`; a tela atual tem `Negocios em aberto`, `Propostas`, `OS em execucao`, `Aguardando cliente`, `Concluidas no mes`.
- Teste verifica `localStorage` (`professional_jobs` e `professional_client_jobs`), mas o componente atual busca dados via API real.

Confianca: 🟢 CONFIRMADO.

## 11. Riscos e lacunas

| Risco/Lacuna | Impacto | Evidencia | Confianca |
|---|---|---|---|
| Testes E2E desatualizados | CI pode falhar ou deixar de validar a tela real | `professional-dashboard.spec.ts` vs `dashboard-principal.tsx` | 🟢 CONFIRMADO |
| Status comerciais hardcoded | Novos status no banco nao aparecem corretamente no funil | `COMMERCIAL_STAGES` em `route.ts:7` | 🟢 CONFIRMADO |
| Status operacionais hardcoded | Novos status de OS ficam fora da agregacao | `OPERATIONAL_STAGES` em `route.ts:8` | 🟢 CONFIRMADO |
| Classificacao por termos | SST/ambiental pode ter falso positivo/negativo | `SST_TERMS`, `AMBIENTAL_TERMS` | 🟢 CONFIRMADO |
| Falha parcial vira falha total | Uma tabela indisponivel impede todo o dashboard | `Promise.all` sem fallback granular | 🟢 CONFIRMADO |
| Valores financeiros como string | Pipeline depende de parser fragil | `ServiceRequest.valor String?` e `parseMoney()` | 🟢 CONFIRMADO |
| Sem cache ou revalidacao | Cada abertura dispara varias consultas | Ausencia de cache/revalidate no endpoint | 🟡 INFERIDO |
| Sem controle explicito de role na API | Endpoint exige login, mas nao valida role profissional diretamente | `route.ts:112-118` | 🟢 CONFIRMADO |

## 12. Recomendações executaveis

1. Atualizar `professional-dashboard.spec.ts` para a nova tela `Dashboard Principal` e para os novos KPIs.
2. Criar testes de contrato para `GET /api/profissional/dashboard`, cobrindo 401, resposta vazia, alertas e parser monetario.
3. Trocar `ServiceRequest.valor` para tipo numerico/decimal em evolucao futura de schema, ou normalizar no momento da escrita.
4. Centralizar status comerciais e operacionais em constantes compartilhadas ou tabela de dominio.
5. Tornar classificacao SST/ambiental mais explicita no modelo, evitando depender apenas de busca textual.
6. Avaliar fallback parcial por bloco: se agenda falhar, ainda retornar KPIs comerciais e operacionais.

## 13. Sumario tecnico

O dashboard principal atual representa uma evolucao importante em relacao a documentacao anterior: ele ja consome uma API propria e agrega dados reais via Prisma. A tela esta bem separada entre UI e endpoint, com bom tratamento basico de loading/erro. Os maiores pontos de atencao estao nos contratos implicitos: status hardcoded, valores monetarios textuais, matching semantico por substring e testes automatizados ainda presos ao dashboard anterior.
