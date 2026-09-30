<!--
section: product-rules-and-guardrails
owner: Product Specialist
approver: Tech Lead
version: 1.0
source_of_truth: docs/novatech/ (Anexo A)
applies_to: Copilot, Claude Code e qualquer agente que gere código, prompt, teste ou spec neste repositório
-->

## Product Rules & Guardrails

> **Para agentes:** esta seção define **o que o NovaTech Assistant pode e não pode responder** e **como o código precisa garantir isso**. Antes de gerar código em `src/functions/query/`, `src/services/`, `prompts/` ou `tests/`, leia esta seção inteira. Em caso de conflito, a precedência é: **NÃO DEVE > DEVE > QUANDO EM DÚVIDA**.

**Convenções desta seção**

- IDs estáveis: `AG-DEVE-nn`, `AG-NDEVE-nn`, `AG-DUVIDA-nn` (comportamento do assistente) e `AG-COD-nn` (restrições de código). Commits, PRs, testes e comentários de código devem citar o ID da regra que implementam.
- **DEVE** e **NÃO DEVE** são obrigatórios. **QUANDO EM DÚVIDA** define o fallback.
- A coluna **Enforcement** diz como a regra é garantida: 🔒 **código** (determinístico, garantido por implementação) ou 💬 **prompt** (probabilístico, instrução ao modelo). Uma regra 🔒 **nunca** pode depender só de instrução no prompt.
- **Fonte de verdade:** os documentos da NovaTech em `docs/novatech/` (Anexo A). O FAQ-Atendimento é **informal** e nunca prevalece sobre POL, PROC ou SLA.

---

### 1. Regras de comportamento do assistente

#### DEVE

| ID | Regra | Enforcement | Onde |
|---|---|---|---|
| **AG-DEVE-01** | Citar a fonte em **toda** resposta com o **identificador do documento, já com a versão, e a seção**. Formato: `<source_document> <section>`. Exemplos: `POL-001 §3.2`, `PROC-042-v2 §2.1`, `SLA-2024 §2`, `FAQ-Atendimento Item 15`. Nunca `PROC-042, seção 2`. | 🔒 código | `response-builder.ts` monta a citação a partir dos metadados do chunk |
| **AG-DEVE-02** | Incluir o campo `source_document` no JSON de retorno **em toda resposta, inclusive com confiança baixa**. Ele só pode ser `null` quando `status = "not_found"`. | 🔒 código | `QueryResponseSchema` (ver §3) |
| **AG-DEVE-03** | Responder em **português formal**: norma culta, tratamento impessoal, sem gírias, sem emojis, sem "a gente". Trechos do FAQ são reescritos em registro formal, sem mudar o sentido, e mantêm a citação. | 💬 prompt + 🔒 lista de bloqueio | `prompts/system-prompt.md`; `response-validator.ts` |
| **AG-DEVE-04** | Em pergunta sobre **devolução de carga perigosa**, responder com os termos da POL-001 §3.2: a carga "NÃO é elegível para devolução pelo processo padrão", e o cliente deve procurar a **Gestão de Riscos (ramal 4500)** "para tratamento individual". *Derivada de AG-NDEVE-02.* | 🔒 código | `response-validator.ts`: se o gatilho de carga perigosa disparar, a resposta tem que conter "processo padrão" e "4500" |
| **AG-DEVE-05** | Reproduzir números **com a unidade do documento**: "7 dias úteis", não "7 dias"; "24h úteis", não "1 dia"; "até 4h" (incidente crítico), não "4h úteis". *Derivada de AG-NDEVE-01.* | 🔒 código | `response-validator.ts`: o par número + unidade tem que existir no chunk |
| **AG-DEVE-06** | Marcar como **"sem respaldo em documento normativo"** todo trecho que vier só do `FAQ-Atendimento`. *Derivada de AG-DEVE-01.* | 🔒 código | `response-builder.ts`: `classification = "informal"` gera o rótulo |

#### NÃO DEVE

| ID | Regra | Enforcement | Onde |
|---|---|---|---|
| **AG-NDEVE-01** | Gerar valores numéricos (prazos, multiplicadores, fatores de peso, SLAs, percentuais, valores em R$) que **não estejam literalmente** nos chunks recuperados. Isso inclui **calcular** (ex.: valor de frete em R$, porque o *valor base* não está na base) e **converter unidades**. | 🔒 código | `response-validator.ts`: todo token numérico da resposta tem que existir num chunk recuperado |
| **AG-NDEVE-02** | Afirmar que **carga perigosa (classes 1 a 6 da ANTT)** pode ser devolvida pelo processo padrão, ou aplicar a ela o prazo geral de **7 dias úteis** (POL-001 §3.1). Também não pode prometer exceção: a exceção só aparece no FAQ Item 3, que é informal. | 🔒 código + 💬 prompt | `response-validator.ts` (gatilho de carga perigosa); `system-prompt.md` |
| **AG-NDEVE-03** | Inventar ou aceitar tiers de cliente. **Só existem `Gold`, `Silver` e `Standard`** (SLA-2024 §1). "Platinum" e qualquer outro nome não existem, e o assistente não atribui SLA a eles. | 🔒 código | Enum `CLIENT_TIERS`; `response-validator.ts` reprova valor de SLA associado a tier fora do enum |
| **AG-NDEVE-04** | Chamar uma versão de documento de "vigente", "oficial" ou "desatualizada". A base **não tem** indicação formal de vigência para a PROC-042 nem para a PROC-042-v2 (cabeçalhos das duas). Use "mais recente" e "anterior", com a data de emissão. *Derivada de AG-DUVIDA-03.* | 🔒 código | `response-validator.ts`: lista de termos bloqueados perto de IDs com versão |
| **AG-NDEVE-05** | Descrever o conteúdo de documentos que **não estão na base**: PROC-088, PROC-043 e a tabela mensal de valor base. O assistente pode dizer que eles existem, mas não pode usá-los como fonte. | 💬 prompt + 🔒 código | `system-prompt.md`; `response-builder.ts` rejeita esses IDs como `source_document` |

#### QUANDO EM DÚVIDA

| ID | Situação | Comportamento | Enforcement | Onde |
|---|---|---|---|---|
| **AG-DUVIDA-01** | Confiança baixa (critério **[A DEFINIR — TL]**) | **Prefixar a resposta** com o aviso fixo: `Atenção: resposta com baixa confiança. Confirme na documentação citada antes de repassar ao cliente.` A resposta **mantém** `source_document` (AG-DEVE-02). | 🔒 código | `response-builder.ts` aplica o prefixo quando `confidence = "low"`. O texto nunca é gerado pelo LLM |
| **AG-DUVIDA-02** | A dúvida exige decisão humana ou a resposta tem confiança baixa | **Sugerir escalação** (`escalation_suggested = true`). Se o documento indicar a área, use a área **do documento**: Gestão de Riscos, ramal 4500 (POL-001 §3.2); Comercial (POL-001 §3.5; SLA-2024 §1 Nota); Diretoria Comercial (PROC-042-v2 §4). Só use "supervisor" quando nenhum documento indicar a área. ⚠️ Ver PEND-02. | 🔒 flag + 💬 texto | `response-builder.ts` (flag); `system-prompt.md` (texto) |
| **AG-DUVIDA-03** | Existem duas versões do mesmo documento nos chunks | **Priorizar a mais recente pela data de emissão do metadado** e **informar que existe versão anterior**, com o ID e a data dela. Hoje isso vale para a PROC-042-v2 (10/11/2023) e a PROC-042 (03/03/2023). Quando aplicável, citar a regra de transição da PROC-042-v2 §5. Preencher `older_versions`. ⚠️ Ver PEND-01. | 🔒 código + 💬 prompt | `prompt-builder.ts` detecta versões pelo metadado `document_family`; `response-builder.ts` preenche `older_versions` |
| **AG-DUVIDA-04** | Nenhum chunk recuperado trata da pergunta | Devolver `status = "not_found"`, `source_document = null` e a mensagem fixa `Não encontrei essa informação na documentação consultada.` **sem chamar o LLM**. Nunca responder "não encontrei" quando um chunk recuperado contém a entidade da pergunta (tier, ID de documento). *Derivada de AG-DEVE-02.* | 🔒 código | `search.ts` (recuperação vazia); `response-validator.ts` |

---

### 2. Glossário de linguagem ubíqua

Termos que um LLM interpretaria errado sem contexto do domínio. **Use exatamente estes significados** em código, prompt, testes, nomes de variáveis e specs. Glossário completo: [`docs/domain/bounded-contexts-e-linguagem-ubiqua.md`](docs/domain/bounded-contexts-e-linguagem-ubiqua.md).

| Termo | Significado no projeto | NÃO confundir com | Fonte | Identificador no código |
|---|---|---|---|---|
| **Cliente Gold** | Tier com contrato anual acima de R$ 500.000 **OU** mais de 200 operações/mês. Revisão semestral. | O metal; programa de fidelidade; cartão | SLA-2024 §1 | `"Gold"` em `CLIENT_TIERS` |
| **Cliente Silver** | Contrato anual entre R$ 100.000 e R$ 500.000 **OU** entre 50 e 200 operações/mês. Revisão semestral. | A prata | SLA-2024 §1 | `"Silver"` |
| **Cliente Standard** | Todos os demais clientes. Revisão anual. | "Processo padrão" (devolução); "frete padrão" | SLA-2024 §1 | `"Standard"` |
| **Tier** | Uma das **3** classificações acima. Não existem outras. | "Platinum", "Bronze" e qualquer outro nome | SLA-2024 §1 Nota | `ClientTier` |
| **SLA** | Compromisso de **tempo de atendimento de chamados** e disponibilidade do portal. | **Prazo de entrega da carga** | SLA-2024 §2 | `sla` |
| **SLA de primeira resposta** | Tempo até o primeiro retorno ao cliente. Chamado geral: 2h, 4h e 8h **úteis** (Gold, Silver, Standard). | SLA de resolução | SLA-2024 §2 | `firstResponse` |
| **SLA de resolução** | Tempo até o problema ser resolvido. Chamado geral: 24h, 48h e 72h **úteis**. | SLA de primeira resposta; "1, 2 e 3 dias" | SLA-2024 §2 | `resolution` |
| **Chamado geral** | Chamado que não é incidente crítico. Os SLAs são contados em **horas úteis** (08h-18h, dias úteis). | Incidente crítico | SLA-2024 §2 e §5 | `ticketType: "general"` |
| **Incidente crítico** | Atende a **pelo menos um** critério: carga com valor declarado acima de R$ 100.000 com status desconhecido há mais de 6h; carga perigosa com irregularidade de documentação ou rastreamento; mais de 5 chamados do mesmo cliente em 24h sobre o mesmo problema; risco à segurança de pessoas. SLAs em **horas** (sem "úteis"). Para Gold, o relógio **não pausa**. | "Prioridade alta" (FAQ Item 27, informal, limite de R$ 50.000) | SLA-2024 §2, §3 e §5 | `ticketType: "critical"` |
| **Horas úteis / dias úteis** | Dias úteis excluem sábados, domingos e feriados nacionais. Horário comercial: 08h-18h. | Horas ou dias corridos | POL-001 §3.1; SLA-2024 §5 | — (nunca converter) |
| **Carga perigosa** | Carga das **classes 1 a 6 da ANTT** (Resolução ANTT nº 5.947/2021): explosivos, gases, líquidos inflamáveis, sólidos inflamáveis, oxidantes e peróxidos, substâncias tóxicas e infectantes. | Carga frágil, cara ou "arriscada" no sentido comum | POL-001 §3.2 | `DANGEROUS_CARGO_TERMS` |
| **Processo padrão (devolução)** | O fluxo da POL-001 §3.3: chamado no Portal do Cliente, CT-e, 3 fotos, triagem, coleta reversa. | Tier Standard | POL-001 §3.3 | — |
| **Prazo de devolução** | Até **7 dias úteis** após o recebimento confirmado no tracking, para pedir a devolução. **Não se aplica** às exceções da POL-001 §3.2. | 7 dias corridos; prazo de entrega | POL-001 §3.1 | — |
| **Triagem** | Até **4 horas úteis** para o atendimento verificar a elegibilidade da devolução. | SLA de primeira resposta | POL-001 §3.3 | — |
| **Coleta reversa** | **Agendada** em até 2 dias úteis após a aprovação. | Coleta **realizada** | POL-001 §3.3 | — |
| **Avaria em trânsito** | Erro da NovaTech: devolução sem custo (POL-001 §3.5). O FAQ Item 38 descreve outro processo ("carga danificada", 48h, sinistros), que é informal e diverge. | Carga danificada como sinônimo exato | POL-001 §3.5; FAQ Item 38 | — |
| **Frete especial** | Frete de carga **acima de 500kg**. | Frete expresso, urgente ou de carga perigosa | PROC-042 §1; PROC-042-v2 §1 | `isSpecialFreight(weightKg)` retorna `weightKg > 500` |
| **Multiplicador regional** | Fator por região de destino. **Tem valores diferentes em cada versão**: v1 (Sul 1.2, Sudeste 1.0, Centro-Oeste 1.3, Nordeste 1.4, Norte 1.6) e v2 (Sul 1.3, Sudeste 1.1, Centro-Oeste 1.4, Nordeste 1.5, Norte 1.8). | Desconto; valor fixo | PROC-042 §2.1; PROC-042-v2 §2.1 | Nunca hardcoded (AG-COD-04) |
| **Fator de peso** | Fator por faixa de peso: v1 1.0 / 1.2 / 1.5; v2 1.0 / 1.15 / 1.4 (faixas 500-1.000kg, 1.001-3.000kg, acima de 3.000kg). | Multiplicador regional | PROC-042 §2; PROC-042-v2 §2 | Nunca hardcoded |
| **Valor base** | Tarifa da tabela mensal de fretes. **Não está na base**, então nenhum valor de frete em R$ pode ser calculado. | Um número que o modelo possa estimar | PROC-042 §2 | — |
| **Prazo de entrega (frete especial)** | Prazo padrão da rota + 2 dias úteis (v1) ou + 3 dias úteis (v2). O "prazo padrão da rota" **não está definido** na base. | O prazo total | PROC-042 §3; PROC-042-v2 §3 | — |
| **Versão mais recente** | A versão com a **data de emissão** mais nova no metadado. **Não** significa "vigente". | Vigente ou oficial | Cabeçalhos PROC-042 e PROC-042-v2 | `document_date` |
| **Documento normativo / contratual / informal** | Classificação do documento: POL-001 normativo; SLA-2024 contratual; FAQ-Atendimento informal, **não validado**. | Fontes de peso igual | Cabeçalhos do Anexo A | `classification` |
| **Gestão de Riscos** | Setor que trata individualmente as exceções de devolução. Ramal 4500. | Seguros ou área financeira | POL-001 §3.2 | — |
| **source_document** | ID do documento **já com a versão** (`PROC-042-v2`, não `PROC-042`). | Nome do arquivo; título | Esta seção | campo do JSON |

---

### 3. Restrições que impactam a geração de código

#### 3.1 Contrato de resposta do query endpoint

Todo retorno de `src/functions/query/handler.ts` **DEVE** passar por `QueryResponseSchema`. Nenhum campo pode ser removido ou renomeado sem atualizar esta seção e `specs/query-endpoint/`.

```ts
// src/shared/types.ts
import { z } from "zod";

// AG-NDEVE-03: lista fechada. Não adicionar valores sem mudança no SLA-2024.
export const CLIENT_TIERS = ["Gold", "Silver", "Standard"] as const;
export type ClientTier = (typeof CLIENT_TIERS)[number];

export const DOCUMENT_CLASSIFICATIONS = ["normativo", "contratual", "informal"] as const;

export const SourceSchema = z.object({
  source_document: z.string().min(1),       // ex.: "PROC-042-v2" (sempre com a versão)
  section: z.string().min(1),               // ex.: "§2.1" | "Item 15"
  chunk_id: z.string().min(1),              // ex.: "PROC-042v2-B"
  classification: z.enum(DOCUMENT_CLASSIFICATIONS),
  document_date: z.string().nullable(),     // ISO 8601; null quando o documento não informa (ex.: FAQ)
});

export const QueryResponseSchema = z
  .object({
    status: z.enum(["answered", "not_found"]),
    answer: z.string().min(1),
    source_document: z.string().nullable(), // AG-DEVE-02: SEMPRE presente
    source_section: z.string().nullable(),
    sources: z.array(SourceSchema),
    confidence: z.enum(["high", "low"]),
    low_confidence_warning: z.string().nullable(),
    escalation_suggested: z.boolean(),
    older_versions: z.array(z.string()),    // AG-DUVIDA-03: ex.: ["PROC-042"]
  })
  .superRefine((r, ctx) => {
    if (r.status === "answered" && (r.source_document === null || r.sources.length === 0)) {
      ctx.addIssue({ code: z.ZodIssueCode.custom, message: "AG-DEVE-02: resposta sem source_document" });
    }
    if (r.status === "not_found" && r.source_document !== null) {
      ctx.addIssue({ code: z.ZodIssueCode.custom, message: "AG-DUVIDA-04: not_found com source_document" });
    }
    if (r.confidence === "low" && (!r.low_confidence_warning || !r.answer.startsWith(r.low_confidence_warning))) {
      ctx.addIssue({ code: z.ZodIssueCode.custom, message: "AG-DUVIDA-01: baixa confiança sem prefixo" });
    }
  });

export type QueryResponse = z.infer<typeof QueryResponseSchema>;
```

#### 3.2 Regras de código

| ID | Restrição | Arquivos |
|---|---|---|
| **AG-COD-01** | Todo retorno do query endpoint é validado com `QueryResponseSchema.parse()` antes de sair do handler. Resposta que não passa no schema **não** é devolvida ao canal. | `src/functions/query/handler.ts` |
| **AG-COD-02** | **A citação é montada por código a partir dos metadados do chunk, nunca extraída do texto do LLM.** O LLM devolve só `used_chunk_ids: string[]`. `source_document`, `section` e `classification` vêm do índice. | `src/functions/query/response-builder.ts`, `src/services/completion.ts` |
| **AG-COD-03** | `response-validator.ts` reprova a resposta quando: (a) um token numérico não existe literalmente em nenhum chunk recuperado (AG-NDEVE-01); (b) o gatilho de carga perigosa dispara e faltam "processo padrão" ou "4500" (AG-DEVE-04); (c) aparece um tier fora de `CLIENT_TIERS` associado a um valor de SLA (AG-NDEVE-03); (d) aparecem "vigente", "oficial" ou "desatualizada" junto de um ID versionado (AG-NDEVE-04). A ação em caso de reprovação está em PEND-03. | `src/services/response-validator.ts` |
| **AG-COD-04** | **Proibido hardcodar valores de negócio** (multiplicadores, fatores de peso, SLAs, prazos, percentuais de desconto) em `src/`. Esses valores só existem nos documentos indexados e em `tests/fixtures/`. A única exceção é o enum `CLIENT_TIERS`. | `src/**` |
| **AG-COD-05** | **Proibido criar função que calcule valor de frete** (ex.: `calculateFreight`, `valorBase * multiplicador * fator`). O valor base não está na base (AG-NDEVE-01). | `src/**` |
| **AG-COD-06** | A escolha entre versões usa **só** o metadado `document_date` e o `document_family` do chunk. Nunca usa nome de arquivo, ordem de recuperação ou sufixo `-v2`. | `src/services/prompt-builder.ts`, `src/services/search.ts` |
| **AG-COD-07** | O aviso de baixa confiança (AG-DUVIDA-01) e a mensagem de `not_found` (AG-DUVIDA-04) são **constantes em código**, com o texto exato desta seção. O LLM não gera esses textos. | `src/functions/query/response-builder.ts` |
| **AG-COD-08** | O gatilho de carga perigosa usa a constante `DANGEROUS_CARGO_TERMS`, com termos da POL-001 §3.2: `carga perigosa`, `explosivo`, `gás`/`gases`, `inflamável`, `oxidante`, `peróxido`, `tóxic`, `infectante`, `classe 1` a `classe 6`. A comparação ignora maiúsculas e acentos. Manutenção da lista: PS. | Arquivo proposto: `src/shared/domain-terms.ts` (ver PEND-04) |
| **AG-COD-09** | Os campos do JSON usam `snake_case` (`source_document`, `older_versions`). Os textos exibidos ao atendente ficam em português formal. Identificadores de código ficam em inglês. | `src/**` |
| **AG-COD-10** | Mudanças no comportamento do assistente acontecem **só** em `prompts/system-prompt.md`, com entrada em `prompts/prompt-changelog.md` (data, autor, motivo, resultado esperado). Não escrever instruções de prompt dentro de arquivos `.ts`. | `prompts/` |
| **AG-COD-11** | Cada regra 🔒 desta seção tem pelo menos um teste em `tests/unit/` ou `tests/integration/`, nomeado com o ID da regra (ex.: `it("AG-NDEVE-02: carga perigosa não usa prazo de 7 dias úteis")`). Os testes usam os chunks de `tests/fixtures/chunks.ts`, derivados de `data/retrieval-corpus/` (Anexo B). | `tests/**` |
| **AG-COD-12** | Toda regra nova ou alterada nesta seção gera uma entrada em `prompts/eval/golden-queries.json` com pergunta, chunks esperados e resposta esperada. | `prompts/eval/golden-queries.json` |

---

### 4. Referências no repositório

| Tipo | Caminho | Uso pelo agente |
|---|---|---|
| Fonte de verdade (Anexo A) | `docs/novatech/` | Consultar antes de escrever qualquer valor, regra ou exemplo |
| Corpus de recuperação (Anexo B) | `data/retrieval-corpus/` | Base para fixtures e golden queries |
| Recorte de domínio e glossário completo | `docs/domain/bounded-contexts-e-linguagem-ubiqua.md` | Significado de termos que não estão no glossário acima |
| Guardrails detalhados (37 regras) | `docs/guardrails.md` | Detalhamento, classificação de enforcement e rastreabilidade aos incidentes |
| Spec do query endpoint | `specs/query-endpoint/requirements.md` · `plan.md` · `tasks.md` | Outcomes, constraints e Verification Criteria (VC-nn) |
| Spec do pipeline de ingestão | `specs/pipeline-ingestao/` | Metadados por chunk (`document_family`, `document_date`, `classification`) |
| Spec da API de feedback | `specs/feedback-api/` | — |
| Spec do bot do Teams | `specs/teams-bot/` | Renderização de `low_confidence_warning` e `older_versions` |
| Spec do painel web | `specs/painel-web/` | Mesma renderização do bot |
| ADRs | `docs/adr/` (ADR-0001 a ADR-0004) | Modelo, context budget (3 turnos, 5 chunks), vigência, build × buy |
| System prompt | `prompts/system-prompt.md` · `prompts/prompt-changelog.md` | Onde ficam as regras 💬 |
| Avaliação | `prompts/eval/golden-queries.json` · `prompts/eval/eval-results/` | Regressão das regras |
| Fixtures de teste | `tests/fixtures/chunks.ts` · `queries.ts` · `expected-responses.ts` | Dados dos testes AG-COD-11 |

---

### 5. Pendências

Até serem resolvidas, os agentes **não** devem implementar comportamento para estes pontos além do que está descrito nas regras.

| ID | Pendência | Regras afetadas | Dono |
|---|---|---|---|
| **PEND-01** | **Versões coexistentes.** AG-DUVIDA-03 manda priorizar a mais recente, em linha com a ADR-0003. A spec de RAG anterior manda "mostrar ambas as versões". A PROC-042-v2 não substitui formalmente a v1 (cabeçalho). Falta decidir se a resposta mostra **os valores** da versão anterior ou só informa que ela existe. | AG-DUVIDA-03, AG-NDEVE-04, AG-COD-06 | TL + NovaTech (Compliance) |
| **PEND-02** | **"Supervisor"** não aparece em nenhum documento do Anexo A. Falta definir quem é, e se a escalação para o supervisor vale quando o documento já indica outra área. | AG-DUVIDA-02 | PS + NovaTech |
| **PEND-03** | **Ação quando `response-validator.ts` reprova:** bloquear e devolver `not_found`, regenerar uma vez ou marcar `confidence = "low"`? Regenerar consome parte do limite de 30 segundos. | AG-COD-03 | TL |
| **PEND-04** | **Critério de "confiança baixa"** e **local da constante `DANGEROUS_CARGO_TERMS`**: `src/shared/domain-terms.ts` não existe na estrutura do Anexo C. | AG-DUVIDA-01, AG-COD-08 | TL |
