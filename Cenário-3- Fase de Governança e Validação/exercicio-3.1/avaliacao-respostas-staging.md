# Revisão crítica das respostas do assistente (staging)

> Exercício 3.1 — Revisão Crítica de Outputs de IA. Validação de uma amostra de 6 respostas antes do go-live.

| Campo | Valor |
|---|---|
| **Fonte de verdade** | Anexo A (POL-001, PROC-042, PROC-042-v2, SLA-2024, FAQ-Atendimento) |
| **Apoio** | Anexo B (chunks e armadilhas); [`docs/guardrails.md`](../guardrails.md); seção *Product Rules & Guardrails* do `AGENTS.md` |
| **Avaliadores** | (1) Product Specialist; (2) Claude, como segundo avaliador |
| **Caminho sugerido** | `docs/reviews/2026-09-avaliacao-respostas-staging.md` |

## Sumário

- [Método e escala](#método-e-escala)
- [1. Avaliação do Product Specialist](#1-avaliação-do-product-specialist)
- [2. Avaliação do Claude (segundo avaliador)](#2-avaliação-do-claude-segundo-avaliador)
- [3. Comparação](#3-comparação)
- [4. Padrões transversais](#4-padrões-transversais)
- [5. Tipo de erro e ajustes de produto](#5-tipo-de-erro-e-ajustes-de-produto)
- [6. Pontos de human-in-the-loop propostos](#6-pontos-de-human-in-the-loop-propostos)
- [7. Pendências](#7-pendências)

---

## Método e escala

Cada resposta foi quebrada em **afirmações**. Cada afirmação foi conferida contra o Anexo A (texto e seção) e contra os chunks do Anexo B que seriam recuperados. A fonte citada e a confiança declarada também foram avaliadas, porque fazem parte do que o atendente recebe.

| Veredito | Critério |
|---|---|
| ✅ **Correta** | Todas as afirmações têm respaldo, a citação está certa e a confiança está calibrada |
| 🟡 **Correta com ressalvas** | O conteúdo principal está certo e não induz erro grave, mas há defeito de citação, unidade, completude ou encaminhamento |
| 🟠 **Parcialmente correta / problemática** | O conteúdo reproduz uma fonte, mas a fonte não sustenta a afirmação com o peso dado (ex.: FAQ informal apresentado como regra) |
| ❌ **Incorreta** | Contém afirmação sem respaldo ou contrária ao Anexo A |

---

## 1. Avaliação do Product Specialist

> [!IMPORTANT]
> **A preencher pela Product Specialist, antes de ler a seção 2.** O exercício pede uma avaliação independente. Registre o veredito e a justificativa com base no Anexo A.

| # | Pergunta (resumo) | Veredito do PS | Justificativa do PS |
|---|---|---|---|
| 1 | Prazo de devolução para produtos standard | | |
| 2 | Prazo de resolução do cliente Silver | | |
| 3 | Devolução de carga perigosa classe 3 | | |
| 4 | Política para carga danificada | | |
| 5 | SLA do cliente Enterprise | | |
| 6 | Carga perigosa com frete expresso | | |

---

## 2. Avaliação do Claude (segundo avaliador)

### Resumo

| # | Pergunta | Veredito | Problema principal |
|---|---|---|---|
| 1 | Prazo de devolução para produtos standard | 🟡 Correta com ressalvas | Cita a seção errada: §3.2 (exceções) em vez de §3.1 e §3.3 |
| 2 | Prazo de resolução do cliente Silver | 🟡 Correta com ressalvas | Omite "úteis" (48h úteis ≠ 48h) e não cita a seção |
| 3 | Devolução de carga perigosa classe 3 | 🟡 Correta com ressalvas | Troca o encaminhamento documentado (Gestão de Riscos, ramal 4500) por "supervisor" |
| 4 | Política para carga danificada | ❌ **Incorreta: alucinação** | Inventa uma "política de danos" que não existe na base, sem fonte e com confiança alta |
| 5 | SLA do cliente Enterprise | 🟡 Correta com ressalvas | Não cita a fonte (SLA-2024 §1). A confiança "Baixa" está mal calibrada |
| 6 | Carga perigosa com frete expresso | 🟠 **Problemática: fonte não confiável** | Responde "Sim" sobre carga perigosa com base só no FAQ informal, sem rótulo e com confiança alta |

### Detalhamento

#### Resposta 1 — 🟡 Correta com ressalvas

> "O prazo é de 7 dias úteis após o recebimento. O cliente deve abrir chamado no portal e anexar fotos." · Fonte: POL-001, seção 3.2 · Confiança: Alta

| Afirmação | Anexo A | Resultado |
|---|---|---|
| 7 dias úteis após o recebimento | POL-001 §3.1: "até 7 (sete) dias úteis após a data de recebimento confirmada no sistema de tracking" | ✅ |
| Abrir chamado no portal | POL-001 §3.3, item 1: Portal do Cliente, categoria "Devolução de Mercadoria" | ✅ |
| Anexar fotos | POL-001 §3.3, item 2: "mínimo 3 fotos", além de CT-e e motivo | 🟡 Incompleta: omite o mínimo de 3 fotos, o CT-e e o motivo |
| Vale para "produtos standard" | POL-001 §1: "aplicável a todos os tipos de cliente e categorias de carga, salvo exceções" | ✅ Vale nas duas leituras de "standard" (tier ou carga comum) |
| **Fonte: seção 3.2** | A §3.2 trata das **exceções**. O prazo está na §3.1 e o procedimento na §3.3 | ❌ Citação errada |

**Justificativa:** o conteúdo está certo, mas a citação aponta para a seção que diz o contrário (as exceções). Um atendente que abrir a §3.2 para conferir vai encontrar a lista de cargas que **não** podem ser devolvidas. É o mesmo tipo de defeito do INC-2 (citação imprecisa).

#### Resposta 2 — 🟡 Correta com ressalvas

> "O cliente Silver tem resolução em até 48h." · Fonte: SLA-2024 · Confiança: Alta

| Afirmação | Anexo A | Resultado |
|---|---|---|
| Silver: resolução em até 48h | SLA-2024 §2: "Até 48h **úteis**" (chamados gerais) | 🟡 A unidade foi omitida |
| (não diferencia o tipo de chamado) | SLA-2024 §2: incidente crítico Silver tem resolução "Até 8h" | 🟡 Incompleta |
| **Fonte: SLA-2024** | Falta a seção (§2) | 🟡 Citação incompleta |

**Justificativa:** o número está certo para chamados gerais. Mas "48h" e "48h úteis" são prazos bem diferentes, porque o relógio pausa fora de 08h-18h em dias úteis (SLA-2024 §5). O atendente pode prometer ao cliente um prazo mais curto que o contratual. A resposta também não avisa que, em incidente crítico, o prazo é outro (8h).

#### Resposta 3 — 🟡 Correta com ressalvas

> "Não. Cargas perigosas (classes 1 a 6 da ANTT) não podem ser devolvidas pelo processo padrão. Recomendo escalar para o supervisor." · Fonte: POL-001, seção 3.2 · Confiança: Alta

| Afirmação | Anexo A | Resultado |
|---|---|---|
| Classes 1 a 6 da ANTT não podem ser devolvidas pelo processo padrão | POL-001 §3.2: "NÃO são elegíveis para devolução pelo processo padrão" (classe 3 = líquidos inflamáveis) | ✅ Com a qualificação "pelo processo padrão", correta |
| Fonte: POL-001 §3.2 | É a seção correta | ✅ |
| "Recomendo escalar para o supervisor" | POL-001 §3.2: "o cliente deve entrar em contato com o setor de **Gestão de Riscos** (ramal 4500) para tratamento individual". "Supervisor" não aparece no Anexo A | 🟡 Troca o encaminhamento documentado por um que não existe na base |

**Justificativa:** a regra está certa e bem qualificada, o que evita o INC-1. O defeito é o próximo passo: o documento manda para a Gestão de Riscos, e a resposta manda para um papel que a base não define. Esse comportamento vem do guardrail simulado do Exercício 2.3 ("sugerir escalação ao supervisor"), que já estava registrado como pendência (PEND-02 do `AGENTS.md`).

#### Resposta 4 — ❌ Incorreta: alucinação

> "A política de danos prevê reembolso integral quando comprovada negligência da transportadora, mediante laudo técnico e fotos." · Fonte: Nenhuma · Confiança: Alta

| Afirmação | Anexo A | Resultado |
|---|---|---|
| "A política de danos" | **Não existe** documento formal sobre carga danificada na base (Notas do Anexo A, lacuna 1) | ❌ Documento inventado |
| Reembolso integral | Só no FAQ Item 38 (informal): "se comprovada responsabilidade nossa, reembolsa integralmente" | ❌ Apresentado como política formal, sem fonte |
| "negligência da transportadora" | Não aparece em nenhum documento. O FAQ fala em "responsabilidade nossa", e a NovaTech **é** a transportadora | ❌ Inventado |
| "mediante laudo técnico" (como exigência) | FAQ Item 38: "com fotos e laudo **se possível**" | ❌ Transforma um opcional em obrigatório |
| (omite) | FAQ Item 38: registrar em até 48h, encaminhar para sinistros@novatech.com.br (Jurídico). POL-001 §3.5: avaria em trânsito → devolução sem custo para o cliente | ❌ Omite o que a base tem |
| Fonte: Nenhuma · Confiança: Alta | — | ❌ Pior combinação possível |

**Justificativa:** o assistente descreveu com confiança alta uma política que não existe, sem citar fonte. Parte do texto lembra o FAQ Item 38, mas distorcido: inventa condições ("negligência"), torna obrigatório o que é opcional ("laudo") e omite o prazo de 48h e o canal. É a falha que mais expõe a NovaTech: o atendente repassaria ao cliente uma promessa de reembolso sem base.

#### Resposta 5 — 🟡 Correta com ressalvas

> "O tier Enterprise não foi encontrado na documentação. Os tiers documentados são Gold, Silver e Standard. Sugiro confirmar a classificação ou escalar ao supervisor." · Fonte: — · Confiança: Baixa

| Afirmação | Anexo A | Resultado |
|---|---|---|
| Enterprise não encontrado; tiers são Gold, Silver e Standard | SLA-2024 §1 Nota: "**Não existem** outros tiers além dos três listados acima" | ✅ Correta. Poderia ser mais firme ("não existe", em vez de "não foi encontrado") |
| Confirmar a classificação | FAQ Item 15 (informal): "peça o número do contrato para verificar" | ✅ Coerente |
| "escalar ao supervisor" | SLA-2024 §1 Nota: SLA diferenciado → "encaminhadas ao **Comercial**" | 🟡 Troca o encaminhamento documentado |
| **Fonte: —** | A informação vem do SLA-2024 §1 | ❌ Viola "toda resposta cita fonte" |
| **Confiança: Baixa** | O documento é explícito ("Não existem outros tiers") | 🟡 Mal calibrada: deveria ser alta |

**Justificativa:** é o comportamento certo para a armadilha 3 do Anexo B (não inventa SLA para um tier inexistente). Mas a resposta não cita a fonte que usou e se declara insegura justamente num caso em que o documento é categórico.

#### Resposta 6 — 🟠 Problemática: fonte não confiável

> "Sim, cargas perigosas podem ser enviadas via frete expresso mediante autorização prévia do compliance e documentação ANTT atualizada." · Fonte: FAQ-Atendimento, item 32 · Confiança: Alta

| Afirmação | Anexo A | Resultado |
|---|---|---|
| "Sim, podem ser enviadas via frete expresso" | Só no FAQ Item 32. **Nenhum documento formal** define esse processo (Notas do Anexo A, contradição 4) | 🟠 Sem respaldo normativo |
| Autorização do Compliance e documentação ANTT | FAQ Item 32: "precisa de autorização do Compliance e a documentação ANTT tem que estar atualizada" | ✅ Fiel ao FAQ |
| (omite) | FAQ Item 32: "demora uns 2 dias para conseguir a autorização [...] avise o cliente" | 🟡 Omite a ressalva do próprio FAQ |
| Fonte: FAQ-Atendimento · Confiança: Alta | FAQ: "Documento informal — NÃO validado por Compliance ou Operações" | 🟠 Fonte informal tratada como definitiva |

**Justificativa:** a resposta é fiel ao FAQ, mas o FAQ não é fonte confiável para um tema crítico (carga perigosa), e a resposta não avisa isso. O "Sim" categórico e a confiança alta passam ao atendente uma autorização que nenhum documento normativo dá. É a armadilha 2 do Anexo B.

---

## 3. Comparação

### 3.1 Claude × critérios do exercício

| # | Critério do exercício | Avaliação do Claude | Concordância |
|---|---|---|---|
| 1 | Adequada | 🟡 Correta com ressalvas (citação errada) | ✅ Concorda que é adequada · ⚠️ acrescenta ressalva de citação |
| 2 | Adequada | 🟡 Correta com ressalvas (unidade omitida) | ✅ Concorda que é adequada · ⚠️ acrescenta ressalva de unidade |
| 3 | Adequada | 🟡 Correta com ressalvas (encaminhamento) | ✅ Concorda que é adequada · ⚠️ acrescenta ressalva de encaminhamento |
| 4 | Alucinação | ❌ Incorreta: alucinação | ✅ Concorda |
| 5 | Adequada | 🟡 Correta com ressalvas (sem fonte, confiança) | ✅ Concorda que é adequada · ⚠️ acrescenta ressalva de citação e calibração |
| 6 | Problemática (FAQ informal) | 🟠 Problemática: fonte não confiável | ✅ Concorda |

**Onde há divergência de rigor:** o critério trata 1, 2, 3 e 5 como adequadas. O Claude concorda que nenhuma delas passa um fato errado sobre a regra de negócio, mas registra ressalvas que, pelos guardrails do projeto, são defeitos:

- **Citação:** as respostas 1 e 5 violam DEVE-01 / AG-DEVE-01 (fonte com documento e seção corretos). A resposta 5 viola também AG-DEVE-02 (`source_document` mesmo com confiança baixa).
- **Unidade:** a resposta 2 viola NDEVE-09 / AG-DEVE-05 (unidade exatamente como no documento). É o mesmo defeito secundário do INC-1 ("7 dias" em vez de "7 dias úteis").
- **Encaminhamento:** as respostas 3 e 5 seguem o guardrail simulado ("escalar ao supervisor"), mas deixam de lado o canal que o documento indica.

**Sobre a resposta 6:** o critério diz que a informação "deveria vir de documento formal". Esse documento **não existe** na base. O comportamento correto não é buscar um documento formal, e sim informar que não há documento normativo, apresentar o FAQ com rótulo de informal e acionar o human-in-the-loop (seção 6).

### 3.2 Product Specialist × Claude

> [!IMPORTANT]
> **A preencher depois da seção 1.** Para cada divergência, registre quem está certo segundo o Anexo A, ou se a divergência é de rigor (ressalva × defeito).

| # | Veredito do PS | Veredito do Claude | Concordam? | Análise da divergência |
|---|---|---|---|---|
| 1 | | 🟡 Correta com ressalvas | | |
| 2 | | 🟡 Correta com ressalvas | | |
| 3 | | 🟡 Correta com ressalvas | | |
| 4 | | ❌ Incorreta: alucinação | | |
| 5 | | 🟡 Correta com ressalvas | | |
| 6 | | 🟠 Problemática: fonte não confiável | | |

---

## 4. Padrões transversais

| Padrão | Evidência na amostra | Por que importa |
|---|---|---|
| **A confiança declarada pelo modelo está invertida** | Confiança **alta** na alucinação (4) e na resposta baseada em FAQ (6). Confiança **baixa** no único caso em que o documento é categórico (5) | A confiança autodeclarada pelo LLM não serve como sinal. Se o HITL depender dela, os casos mais perigosos passam direto |
| **A citação falha em 4 de 6 respostas** | Seção errada (1), sem seção (2), nenhuma fonte (4 e 5) | Com texto livre, nada impede a resposta de sair sem fonte. É o problema descrito no cenário 3 |
| **Encaminhamento não documentado em 2 de 6** | "Supervisor" nas respostas 3 e 5, em vez de Gestão de Riscos (POL-001 §3.2) e Comercial (SLA-2024 §1 Nota) | Vem de uma regra de produto (guardrail simulado), não do modelo. A correção é na regra |
| **Temas sensíveis concentram os erros graves** | As duas respostas com erro grave (4 e 6) são sobre dano e carga perigosa | Justifica um HITL por tema, não só por confiança |

A amostra tem 1 incorreta e 1 problemática em 6 respostas. Isso é coerente com os 12% de respostas incorretas relatados no cenário, mas a amostra é pequena demais para medir a taxa.

---

## 5. Tipo de erro e ajustes de produto

**Tipos de erro** (classificação do exercício): **alucinação** · **fonte não confiável** · **informação incompleta**.
**Camadas de ajuste:** 💬 **Prompt** · 🖥️ **Interface** (card do Teams e painel web) · ⚙️ **Pipeline e harness** (ingestão, recuperação, `response-builder`, `response-validator`, schema).

| # | Tipo de erro | Camada | Ajuste proposto | Regras relacionadas |
|---|---|---|---|---|
| **1** | Informação incompleta (citação imprecisa; procedimento resumido) | ⚙️ Harness | A citação passa a ser montada a partir dos metadados do chunk (documento + seção), não escrita pelo modelo. O structured output exige `source_document` e `source_section` | AG-COD-02; DEVE-01 |
| | | 🖥️ Interface | Link "ver trecho da fonte" no card, mostrando o texto do chunk citado, para o atendente conferir em um clique | — |
| **2** | Informação incompleta (unidade omitida; sem tipo de chamado; sem seção) | ⚙️ Harness | O `response-validator` confere o par número + unidade contra o chunk: "48h" sem "úteis" é reprovado | AG-DEVE-05; NDEVE-09 |
| | | 💬 Prompt | Pergunta de SLA sem tipo de chamado → responder chamado geral **e** incidente crítico, rotulados | DEVE-07; DUVIDA-05 |
| | | 🖥️ Interface | Card de SLA em formato de tabela (primeira resposta × resolução; geral × crítico) | — |
| **3** | Informação incompleta (encaminhamento documentado omitido) | 💬 Prompt + regra de produto | Revisar AG-DUVIDA-02: usar primeiro a área que o documento indica (Gestão de Riscos, ramal 4500) e só usar "supervisor" quando nenhum documento indicar. Resolver PEND-02 | AG-DUVIDA-02; DEVE-04 |
| | | ⚙️ Harness | Checagem de AG-DEVE-04: pergunta sobre devolução de carga perigosa → a resposta tem que conter "4500" | AG-DEVE-04; AG-COD-03 |
| | | 🖥️ Interface | Botão de ação com o contato documentado ("Gestão de Riscos — ramal 4500") | — |
| **4** | **Alucinação** (política inventada, sem fonte, confiança alta) | ⚙️ Harness | **Structured output obrigatório:** `status = "answered"` com `sources` vazio é **rejeitado pelo schema**. Esta resposta nunca chegaria ao atendente | AG-DEVE-02; AG-COD-01; `QueryResponseSchema` |
| | | ⚙️ Harness | **Confiança calculada por código, não autodeclarada** (ver padrão 1 da seção 4): alta só com fonte normativa ou contratual citada e validação aprovada | Proposta nova (PEND-A) |
| | | ⚙️ Pipeline | Para "carga danificada" ou "avaria", recuperar também o POL-001-D (§3.5, avaria em trânsito) e o FAQ-38 com classificação informal | AG-DEVE-06 |
| | | 💬 Prompt | Sem trecho que sustente a afirmação → "Não encontrei essa informação na documentação consultada." Proibido nomear documentos ou políticas que não estejam nos trechos | NDEVE-01; AG-NDEVE-05 |
| | | 👤 HITL | Tema sensível sem fonte normativa → não responde direto (seção 6) | HITL-01 |
| **5** | Informação incompleta (sem citação) + confiança mal calibrada | ⚙️ Harness | `source_document` obrigatório mesmo com confiança baixa (schema). Confiança calculada por código: um documento explícito gera confiança alta | AG-DEVE-02; PEND-A |
| | | 💬 Prompt | Tier inexistente → citar SLA-2024 §1 e encaminhar SLA diferenciado ao **Comercial** | AG-NDEVE-03 |
| **6** | **Fonte não confiável** (FAQ informal como base de resposta categórica sobre carga perigosa) | ⚙️ Harness | Se **todas** as fontes citadas forem informais → rótulo "sem respaldo em documento normativo" inserido por código e confiança forçada para baixa | AG-DEVE-06; DUVIDA-04 |
| | | ⚙️ Pipeline | Metadado `classification` por chunk. Em perguntas sobre carga perigosa, a busca prioriza documentos normativos | AG-COD-06 |
| | | 💬 Prompt | Proibido começar com "Sim" ou "Não" quando a única fonte é informal. Reproduzir a ressalva do próprio FAQ ("demora uns 2 dias") | NDEVE-08 |
| | | 🖥️ Interface | Selo "Informal — não validado" no card, em cor de alerta | — |
| | | 👤 HITL | Tema sensível + fonte só informal → human-in-the-loop (seção 6) | HITL-01 |

---

## 6. Pontos de human-in-the-loop propostos

**Restrição de desenho:** o atendente precisa da resposta em menos de 30 segundos. Uma aprovação humana síncrona em toda resposta sensível quebraria essa meta. Por isso o HITL proposto tem três níveis:

| ID | Gatilho (calculado por código) | Ação | Quem valida |
|---|---|---|---|
| **HITL-01** | Tema sensível (carga perigosa, dano/avaria/sinistro, exceção de devolução, desconto) **E** (sem fonte **OU** só fonte informal **OU** falha no `response-validator`) | A resposta **não** é exibida como resposta. O card mostra: "Esta pergunta exige confirmação. Não há documento normativo que a responda." e o encaminhamento documentado, se houver | O atendente decide com base no encaminhamento. A área responsável fica **[A DEFINIR — NovaTech]** |
| **HITL-02** | Confiança baixa **calculada por código** (fora do HITL-01) | A resposta é exibida com o aviso de baixa confiança e o botão "confirmar antes de repassar" | O atendente (humano no ponto de uso) |
| **HITL-03** | Todas as respostas que caírem em HITL-01 ou HITL-02, mais uma amostra das demais | Fila de revisão assíncrona, alimentada pela API de feedback. Os casos confirmados viram golden queries | Product Specialist + QA. Tamanho da amostra: **[A DEFINIR]** |

Com esses gatilhos, as respostas 4 e 6 cairiam no **HITL-01**. As respostas 1, 2, 3 e 5, com as correções da seção 5, sairiam direto com a citação correta.

---

## 7. Pendências

| ID | Pendência | Dono |
|---|---|---|
| **PEND-A** | Fórmula da **confiança calculada por código** (substitui a autodeclarada). Proposta inicial: alta = fonte normativa ou contratual citada + validação aprovada; baixa = qualquer outro caso. Alinhar também o nome do campo: `confidence` (AGENTS.md) × `confidence_score` (cenário 3) | TL |
| **PEND-B** | Quem valida as respostas do HITL-01 na NovaTech e qual o canal | PS + NovaTech |
| **PEND-C** | Lista de **temas sensíveis** do HITL-01: manter e revisar junto com `DANGEROUS_CARGO_TERMS` | PS |
| **PEND-02** (AGENTS.md) | "Supervisor" não existe no Anexo A. Esta revisão mostra o impacto real: as respostas 3 e 5 omitiram o canal documentado | PS + NovaTech |
