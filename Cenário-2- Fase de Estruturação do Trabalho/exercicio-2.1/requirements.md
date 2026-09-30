# requirements.md — Query Endpoint (NovaTech Assistant)

| Campo | Valor |
|---|---|
| **Versão** | v1.1: v1 complementada após a conferência do Exercício 2.1, item 2 (ver [Changelog](#changelog-v1--v11)) |
| **Caminho no repositório** | `specs/query-endpoint/requirements.md` |
| **Módulo** | Query endpoint: recebe a pergunta do atendente e devolve a resposta com fonte |
| **Componente da arquitetura** | (2) API do assistente: Azure Functions + Azure AI Search + Azure OpenAI; código em `src/functions/query/` |
| **Autoria e aprovação** | Escrito pelo Product Specialist, aprovado pelo Tech Lead (convenção do Anexo C) |
| **Fontes** | Anexo A (fonte de verdade); Anexo B (chunks de referência); Anexo C (estrutura do repositório) |
| **Recorte de domínio** | [Bounded contexts e linguagem ubíqua](../../docs/domain/bounded-contexts-e-linguagem-ubiqua.md) |
| **Convenção** | Termos em *itálico* seguem o glossário. **[A DEFINIR — validar com TL/NovaTech]** marca um valor que não existe no contexto fornecido. |

### Changelog v1 → v1.1

Estas mudanças são complementos de conferência. Os apontamentos da revisão do Tech Lead (TL-01 a TL-20) **não** foram aplicados: eles entram na v2, depois da aprovação.

| # | Mudança | Motivo |
|---|---|---|
| 1 | Cabeçalho com caminho, componente e código-fonte | Cenário (arquitetura em 3 componentes) e Anexo C |
| 2 | O-03 ligado aos cruzamentos documentados | Recorte de domínio §1.9 |
| 3 | Scope 2.1 aponta para as seções de origem; 2.2 usa os slugs de `specs/` | Item 2: "scope boundaries devem derivar dos bounded contexts"; Anexo C |
| 4 | ADR-0004 cita o protótipo (ChromaDB + sentence-transformers) | Cenário |
| 5 | Tensões T-05 a T-07 | Anexo B (gabarito), cenário (63 descartados) |
| 6 | VC-20 a VC-23 e rastreabilidade VC → chunks | Anexo B (mapa de cobertura e armadilhas) |
| 7 | OQ-17 a OQ-21 | Itens 5 e 6 |

---

## 1. Outcomes

- **O-01.** O atendente consegue uma resposta a uma pergunta sobre *prazos de entrega*, *regras de frete*, *política de devolução* ou *SLAs* **em menos de 30 segundos**.
- **O-02.** O atendente consegue saber, **em cada afirmação da resposta**, de qual documento e seção ela veio, e pode repassar isso ao cliente com segurança.
- **O-03.** Quando a pergunta cruza duas categorias (cerca de 15% dos casos, segundo o discovery), o atendente recebe **uma resposta única** que trata as duas partes, cada uma com sua própria fonte, sem ter que perguntar duas vezes. Os cruzamentos que a base documenta estão no [recorte de domínio §1.9](../../docs/domain/bounded-contexts-e-linguagem-ubiqua.md#19-perguntas-que-cruzam-categorias-os-15-do-discovery).
- **O-04.** Quando os documentos se contradizem (ex.: *PROC-042 v1* × *PROC-042-v2*), o atendente vê **as duas versões lado a lado**, identificadas por documento e versão, e sabe que existe uma contradição sem resolução formal.
- **O-05.** O atendente consegue distinguir **regra normativa ou contratual** (POL-001, PROC-042, PROC-042-v2, SLA-2024) de **prática informal** (FAQ-Atendimento), porque o conteúdo vindo só do FAQ chega marcado como *sem respaldo em documento normativo*.
- **O-06.** Quando a base não tem a resposta (ex.: *frete* abaixo de 500kg, *valor base*, *prazo padrão da rota*), o atendente recebe o aviso explícito de que **não há documentação**, em vez de uma resposta plausível e inventada.
- **O-07.** Quando a resposta exige ação fora do atendimento, o atendente sabe **para onde encaminhar**, conforme a base: *Gestão de Riscos* ramal 4500 (POL-001 §3.2), Comercial (POL-001 §3.5; SLA-2024 §1 Nota) ou gerente de operações regional (PROC-042 §4).
- **O-08.** Quando o cliente cita um termo que não existe na base (ex.: tier *Platinum*), o atendente recebe a correção com a fonte ("Não existem outros tiers", SLA-2024 §1 Nota) e a orientação do que pedir ao cliente.

---

## 2. Scope Boundaries

### 2.1 Por bounded context

Derivado das §1.2 a §1.6 (contextos) e da §1.8 (fronteira do assistente) do [recorte de domínio](../../docs/domain/bounded-contexts-e-linguagem-ubiqua.md).

| Bounded context | Relação deste módulo | O que isso significa |
|---|---|---|
| **Atendimento ao Cliente** | **Cobre** | É o contexto deste módulo: recebe a pergunta do atendente, monta a resposta, cita fontes, sinaliza contradição, lacuna e conteúdo informal, e indica o encaminhamento documentado. |
| **Devoluções** | **Consome** | Lê as regras da POL-001 (prazo, exceções, procedimento, custos) já indexadas. Não altera nem interpreta a política além do texto. |
| **Frete e Prazos de Entrega** | **Consome** | Lê a PROC-042 e a PROC-042-v2 e expõe multiplicadores, fator de peso e prazo adicional **das duas versões**. **Não calcula valor final em R$**, porque o *valor base* não está na base. |
| **SLAs e Contratos** | **Consome** | Lê tiers, tabela de SLAs, definição de *incidente crítico*, penalidades e medição. **Não descobre o tier** de um cliente: o tier vem da pergunta do atendente. |
| **Gestão Documental** | **Consome metadados; não cobre** | Usa vigência, classificação (normativo, contratual ou informal), versão e marcação de contradição ou obsolescência produzidas pela ingestão. Não decide vigência, não marca documentos e não resolve contradições (isso é do Compliance). |
| **Contextos externos** (Gestão de Riscos, Comercial, Jurídico/Sinistros, Compliance, PROC-088, PROC-043) | **Não cobre** | Só cita o encaminhamento e a existência do documento quando a base cita. Nunca descreve o conteúdo desses contextos. |

### 2.2 Fronteira com os outros módulos do projeto

Os 5 módulos têm pasta própria em `specs/` (Anexo C). Este módulo é o `query-endpoint`.

| Módulo | Responsabilidade (fora deste módulo) | Interface com o query endpoint |
|---|---|---|
| **Pipeline de ingestão** (`specs/pipeline-ingestao/`) | Consolidação dos 847 documentos, chunking, embeddings, metadados de vigência e classificação, marcação de obsoletos (ADR-0003) e atualização em até 24h (spec anterior). | O query endpoint lê o índice e os metadados. Não reindexa nem corrige chunking. |
| **API de feedback** (`specs/feedback-api/`) | Captura a avaliação do atendente sobre a resposta. | O query endpoint devolve um identificador da resposta para correlação. Formato: **[A DEFINIR — validar com TL/NovaTech]**. |
| **Bot do Microsoft Teams** (`specs/teams-bot/`) | Canal de conversa, autenticação no Teams e renderização da resposta. | Envia a pergunta e o histórico e recebe a resposta estruturada. |
| **Painel web interno** (`specs/painel-web/`) | Canal web, renderização e visualização das fontes. | Mesmo contrato do bot. |

### 2.3 Fora de escopo deste módulo

- Executar ações: abrir chamado, consultar tracking, consultar o Azure DevOps, consultar contrato do cliente.
- Calcular valor final de frete, data exata de entrega ou prazo de SLA em data e hora do calendário. *(A decisão sobre calcular datas está nas Open Questions.)*
- Responder perguntas fora das 4 categorias do discovery. O comportamento nesse caso é **[A DEFINIR — validar com TL/NovaTech]**.
- Escolher entre versões contraditórias (ver Prior Decisions: tensão ADR-0003 × spec).

---

## 3. Constraints

**Comportamento geral (spec de RAG anterior)**

- **C-01. Nunca inventar informações.** Nenhum valor, prazo, percentual, regra, nome de área, contato ou documento pode aparecer na resposta se não estiver nos chunks recuperados.
- **C-02. Toda resposta cita fonte**, no mínimo com **documento e seção** (ex.: `POL-001 §3.2`, `FAQ Item 15`).
- **C-03. Fontes contraditórias mostram ambas as versões**, cada uma com documento e versão, sem declarar vencedora.
- **C-04. Atualização em até 24h.** Responsabilidade do pipeline de ingestão. O query endpoint deve refletir o índice atual, sem cache de respostas que ultrapasse esse prazo. Política de cache: **[A DEFINIR — validar com TL/NovaTech]**.

**Regras de domínio (Anexo A, com os termos exatos do glossário)**

- **C-05. Tiers:** só existem *Gold*, *Silver* e *Standard* ("Não existem outros tiers além dos três listados acima", SLA-2024 §1 Nota). Pergunta sobre outro tier → informar que ele não existe. Nunca atribuir SLA a um tier inexistente.
- **C-06. Carga perigosa** = "classes 1 a 6 da ANTT" (POL-001 §3.2). Ela **não é elegível para devolução pelo processo padrão**. A resposta deve indicar "tratamento individual" com a *Gestão de Riscos* (ramal 4500) e não pode afirmar que a devolução é permitida nem que é impossível.
- **C-07. Frete especial** = carga "acima de 500kg" (PROC-042 §1; v2 §1). Abaixo disso, informar que não há documento de frete na base e não aplicar os multiplicadores da PROC-042.
- **C-08. Valor base** não está na base (tabela mensal externa). Nunca apresentar valor de frete em R$.
- **C-09. Unidades preservadas:** *dias úteis*, *horas úteis* e horas sem "úteis" (*incidente crítico*) são reproduzidas exatamente como no documento. É proibido converter "24h úteis" em "1 dia" ou "7 dias úteis" em "uma semana".
- **C-10. Primeira resposta ≠ resolução; chamado geral ≠ incidente crítico; triagem de devolução (4 horas úteis) ≠ tempo de primeira resposta.** Se a pergunta for ambígua ("qual o SLA?"), a resposta traz as métricas separadas e nomeadas.
- **C-11. Incidente crítico** só é caracterizado pelos critérios do SLA-2024 §3. O limite de "R$ 50.000" do FAQ Item 27 é *prioridade alta*, não *incidente crítico*.
- **C-12. FAQ-Atendimento é informal** ("NÃO validado por Compliance ou Operações"). Conteúdo que só existe no FAQ vem marcado como *sem respaldo em documento normativo*. Quando o FAQ e um documento normativo tratam do mesmo ponto, o normativo aparece primeiro. Se eles se contradizem, vale a C-03.
- **C-13. Documentos citados mas fora da base** (PROC-088, PROC-043, tabela de valor base) podem ser mencionados pelo nome, **nunca** pelo conteúdo.
- **C-14. Documentos com contradição pendente no Compliance** (12 na base) devem vir sinalizados como tal quando forem citados. O formato do sinal é **[A DEFINIR — validar com TL/NovaTech]**.
- **C-15. Idioma e vocabulário:** a resposta usa os termos do glossário (ex.: "coleta reversa é **agendada**", não "realizada"; "reembolso **processado**", não "creditado").

**Desempenho**

- **C-16. Menos de 30 segundos** do recebimento da pergunta até a entrega da resposta (discovery). Percentil e ponto de medição: **[A DEFINIR — validar com TL/NovaTech]**.

---

## 4. Prior Decisions

| ADR | Decisão | O que impõe a este módulo |
|---|---|---|
| **ADR-0001** | Azure OpenAI (GPT-4o), escolhido pela integração com o ecossistema Microsoft e pela janela de 128K tokens. | O endpoint chama o GPT-4o no Azure OpenAI. Não pode trocar de provedor ou modelo sem uma nova ADR. A janela de 128K **não** autoriza passar do orçamento da ADR-0002. |
| **ADR-0002** | Context budget: ~4K tokens de system prompt + ~8K de chunks (5 chunks de ~1.500 tokens) + pergunta + histórico limitado a 3 turnos. | O system prompt (com as Constraints C-01 a C-15) tem que caber em ~4K. Cada resposta usa **no máximo 5 chunks**. Perguntas multi-domínio e contradições (v1 + v2) disputam esses 5 chunks. O histórico enviado ao modelo é truncado em 3 turnos. |
| **ADR-0003** | Metadado de vigência no pipeline. O prompt instrui o modelo a priorizar a versão mais recente. Documentos obsoletos são marcados, não excluídos. | O endpoint lê o metadado de vigência de cada chunk e o system prompt contém a instrução de priorização. Chunks de documentos marcados como obsoletos podem ser recuperados e devem vir identificados como obsoletos. |
| **ADR-0004** | Azure AI Search + Azure OpenAI. O protótipo open-source (ChromaDB + sentence-transformers) validou a abordagem e identificou problemas de chunking em tabelas. | A recuperação usa o Azure AI Search. As respostas que dependem de tabelas (multiplicadores regionais, tabela de SLAs, tiers) têm risco conhecido de chunk incompleto, e os Verification Criteria cobrem isso explicitamente (VC-03, VC-06, VC-07). |

### 4.1 Tensões registradas (NÃO resolvidas neste documento)

- **T-01. ADR-0003 × spec anterior.** A ADR-0003 instrui "priorizar a versão mais recente". A spec diz "fontes contraditórias devem mostrar ambas as versões". Para PROC-042 v1 × v2, as duas instruções levam a respostas diferentes. → Open Question OQ-01.
- **T-02. ADR-0003 × Anexo A.** A ADR-0003 depende de "metadado de vigência", mas nem a PROC-042 nem a PROC-042-v2 têm indicação formal de vigência (cabeçalhos). Não está claro qual valor o pipeline grava. → OQ-02.
- **T-03. ADR-0002 × O-03 e O-04.** Com 5 chunks, uma pergunta que cruza Devoluções e Frete *e* exige as duas versões da PROC-042 precisa de pelo menos 3 fontes diferentes (POL-001, PROC-042, PROC-042-v2). Se a tabela se dividir em mais chunks (ADR-0004), o limite pode não bastar. → OQ-03.
- **T-04. ADR-0002 × histórico.** Um atendente que volta a um assunto de mais de 3 turnos atrás perde o contexto. → OQ-04.
- **T-05. Gabarito do Anexo B × spec anterior.** Para "Frete para 600kg para Manaus?" e "Qual o multiplicador para o Sudeste?", o mapa de cobertura do Anexo B exige só os chunks da v2 e trata a v1 como "risco de contradição". Isso segue a ADR-0003, enquanto VC-03, VC-12 e VC-21 seguem a spec ("mostrar ambas"). → OQ-17.
- **T-06. ADR-0002 × VC-03.** Para mostrar as duas versões completas (fórmula, multiplicador, prazo e transição), o VC-03 precisaria de 7 chunks do Anexo B (PROC-042-A, B, C; PROC-042v2-A, B, C, E), acima do limite de 5. → OQ-03.
- **T-07. ADR-0003 × cenário.** A ADR-0003 diz que obsoletos são "marcados, não excluídos". O cenário diz que 63 documentos foram "descartados por obsolescência". Não está claro se o endpoint pode recuperar chunks desses documentos. → OQ-18.

---

## 5. Verification Criteria

**Regras gerais de aprovação (valem para todos os VCs):**
(a) cada afirmação factual da resposta tem citação de documento e seção, e o trecho citado contém a afirmação (conferência manual do QA contra o Anexo A);
(b) a resposta não contém nenhum valor numérico ausente do Anexo A;
(c) o tempo de resposta fica abaixo de 30 s (ver C-16).

| ID | Entrada (pergunta do atendente) | Resultado esperado | Critério de aprovação |
|---|---|---|---|
| **VC-01** | "Posso devolver carga perigosa?" | Informa que *carga perigosa* (classes 1 a 6 da ANTT) **não é elegível para devolução pelo processo padrão** e que o cliente deve procurar a *Gestão de Riscos*, ramal 4500, para *tratamento individual*. Se usar o FAQ Item 3, esse trecho vem marcado como sem respaldo normativo. | Cita `POL-001 §3.2`. Contém "classes 1 a 6", "Gestão de Riscos" e "4500". **Não** contém "sim, pode" nem "é impossível/proibido". |
| **VC-02** | "Qual o SLA do cliente Platinum?" | Informa que não existe tier Platinum e que os tiers são Gold, Silver e Standard. Orienta encaminhar SLA diferenciado ao Comercial. Pode sugerir pedir o número do contrato (FAQ Item 15, marcado como informal). | Cita `SLA-2024 §1`. **Nenhum** valor de SLA aparece associado a "Platinum". |
| **VC-03** | "Frete para 600kg para Manaus?" | Identifica *frete especial* (acima de 500kg). Mostra **as duas versões**: v1 com multiplicador Norte 1.6 e prazo + 2 dias úteis; v2 com multiplicador Norte 1.8 e prazo + 3 dias úteis; fator de peso 1.0 nas duas. Menciona a regra de transição do v2 §5. Informa que o *valor base* está na tabela mensal e não na base, então não há valor em R$. | Cita `PROC-042 §2.1` **e** `PROC-042-v2 §2.1`. Contém 1.6 e 1.8. Contém "+2" e "+3 dias úteis". **Não** contém valor em R$. Mapear Manaus para Norte: **[A DEFINIR — validar com TL/NovaTech]** (ver OQ-05). |
| **VC-04** | "Frete para 300kg para Salvador?" | Informa que carga de 300kg não é frete especial (PROC-042 só cobre carga acima de 500kg) e que **não há documento de frete padrão na base**. | Cita `PROC-042 §1`. **Não** contém multiplicador regional (1.4, 1.5 etc.) nem valor em R$. |
| **VC-05** *(multi-domínio: Devoluções + Frete)* | "Cliente desistiu de uma carga de 800kg que recebeu anteontem. Ele pode devolver e quem paga o frete de volta?" | Pode, dentro do prazo de 7 dias úteis após o recebimento confirmado no tracking. Por ser desistência, o custo do frete reverso é do cliente, "calculado com os mesmos multiplicadores do frete original". Como 800kg é frete especial, a resposta aponta que há duas versões de multiplicadores (v1 e v2) e não escolhe entre elas. | Cita `POL-001 §3.1`, `POL-001 §3.5` e pelo menos uma das `PROC-042 §2.1` / `PROC-042-v2 §2.1`. Trata as duas partes numa única resposta. |
| **VC-06** | "Qual o SLA de incidente crítico para cliente Gold?" | Primeira resposta até 30min e resolução até 4h. O relógio **não pausa** fora do horário comercial. | Cita `SLA-2024 §2` e `§5`. Contém "30min" e "4h" **sem** a palavra "úteis". |
| **VC-07** | "Qual o prazo do cliente Standard para chamado geral?" | Separa as métricas: primeira resposta até 8h úteis e resolução até 72h úteis. Indica que o relógio pausa fora de 08h-18h em dias úteis. | Contém "8h úteis" **e** "72h úteis" rotulados como resposta e resolução. **Não** contém "3 dias". |
| **VC-08** | "Carga de R$ 150.000 está sem status há 7 horas, cliente Silver. Qual o SLA?" | Classifica como *incidente crítico* (valor declarado acima de R$ 100.000 e status desconhecido há mais de 6 horas). SLA Silver crítico: primeira resposta até 1h e resolução até 8h. Aponta que a base não diz se o relógio pausa para Silver. | Cita `SLA-2024 §3` e `§2`. Contém "incidente crítico", "1h" e "8h". |
| **VC-09** | "Carga de R$ 60.000 parada há 4 dias, cliente Standard. É incidente crítico?" | Não é incidente crítico pelo critério de valor (exige acima de R$ 100.000). Se citar o FAQ Item 27 (prioridade alta acima de R$ 50.000), marca como sem respaldo normativo e como diferente de incidente crítico. | Cita `SLA-2024 §3`. **Não** classifica como "incidente crítico". |
| **VC-10** *(devolução × carga danificada)* | "A carga chegou avariada. Como o cliente devolve?" | Mostra a POL-001 §3.5 (avaria em trânsito → devolução sem custo) **e** o FAQ Item 38 (processo diferente, 48h, sinistros@novatech.com.br), este marcado como sem respaldo normativo. Sinaliza a divergência. | Cita `POL-001 §3.5` **e** `FAQ Item 38`. O trecho do FAQ tem o rótulo informal. |
| **VC-11** *(só FAQ)* | "Quanto custa o seguro de carga?" | Informa 0,3% e 0,8% **com o rótulo "sem respaldo em documento normativo"** e a ressalva do próprio FAQ (contratos a partir de 2023; confirmar com o Comercial). | Cita `FAQ Item 22`. Contém o rótulo informal. |
| **VC-12** *(contradição de desconto)* | "Cliente faz 12 fretes especiais por mês. Tem desconto?" | Mostra a v1 (mais de 10 por mês, negociado pelo Comercial com aditivo) **e** a v2 (a partir de 8 por mês, 5% sobre o multiplicador; acima de 15, 10%). Se usar o FAQ Item 45, marca como informal. | Cita `PROC-042 §4` **e** `PROC-042-v2 §4`. **Não** afirma "desconto automático" sem o rótulo informal. |
| **VC-13** *(fora da base)* | "Como intercepto uma carga que ainda está em trânsito?" | Informa que a POL-001 não se aplica a carga em trânsito e remete à PROC-088, **que não está na base**. | Cita `POL-001 §2`. **Não** descreve passos de interceptação. |
| **VC-14** *(borda: cadeia de frio)* | "Carga refrigerada ficou 20 minutos fora da temperatura. Pode devolver?" | A exceção exige mais de 30 minutos contínuos. Por esse critério, a carga não está excluída, e a resposta indica o processo padrão (POL-001 §3.3). | Cita `POL-001 §3.2`. Contém "30 minutos contínuos". **Não** declara a carga inelegível. |
| **VC-15** *(unidade)* | "Até quando o cliente pode pedir devolução?" | 7 dias úteis após o recebimento confirmado no tracking, com a definição de dias úteis (exclui sábados, domingos e feriados nacionais). | Cita `POL-001 §3.1`. Contém "dias úteis". **Não** contém "7 dias corridos" nem "uma semana". |
| **VC-16** *(histórico)* | Sequência de 4 turnos: T1 "Qual o multiplicador para o Norte?"; T2 e T3 perguntas sobre SLA; T4 "E na outra versão?" | O T1 está fora da janela de 3 turnos. O assistente pede esclarecimento ou declara que falta contexto e não inventa a referência. | Resposta ao T4 **não** contém multiplicador sem que o atendente reformule. Comportamento exato: **[A DEFINIR — validar com TL/NovaTech]**. |
| **VC-17** *(fora de escopo)* | "Qual a previsão do tempo em Manaus amanhã?" | Recusa ou redireciona, conforme política **[A DEFINIR — validar com TL/NovaTech]**. | Resposta **sem** conteúdo meteorológico. |
| **VC-18** *(borda: 500kg)* | "Frete para exatamente 500kg para o Sul?" | **[A DEFINIR — validar com TL/NovaTech]** (ver OQ-06). | Bloqueado até a resposta da NovaTech. |
| **VC-19** *(latência)* | Conjunto VC-01 a VC-15 executado **[A DEFINIR]** vezes. | Todas as respostas em menos de 30 s. | Percentil e ponto de medição **[A DEFINIR — validar com TL/NovaTech]**. |

### 5.1 VCs complementares (perguntas do Anexo B)

Estas perguntas estão no mapa de cobertura ou nas armadilhas do Anexo B e ainda não tinham VC.

| ID | Entrada (pergunta do atendente) | Resultado esperado | Critério de aprovação |
|---|---|---|---|
| **VC-20** *(só FAQ, pergunta crítica)* | "Carga perigosa com frete expresso?" | Só o FAQ Item 32 trata disso. A resposta traz o conteúdo **com o rótulo "sem respaldo em documento normativo"** e não apresenta a autorização do Compliance como regra oficial. | Cita `FAQ Item 32` com rótulo informal. **Não** cita POL, PROC ou SLA como fonte desse processo. |
| **VC-21** *(contradição de tabela)* | "Qual o multiplicador para o Sudeste?" | Sudeste: 1.0 na v1 e 1.1 na v2, cada valor atribuído à sua versão. | Cita `PROC-042 §2.1` **e** `PROC-042-v2 §2.1`. Contém 1.0 e 1.1 com a versão ao lado de cada um. **Não** mistura valores das duas tabelas na mesma frase sem identificação. (Sujeito à T-05.) |
| **VC-22** *(tier sem tipo de chamado)* | "Qual o SLA do cliente Gold?" | Chamados gerais: primeira resposta até 2h úteis, resolução até 24h úteis. Se incluir incidentes críticos (30min / 4h), rotula separadamente. | Cita `SLA-2024 §2`. Contém "2h úteis" e "24h úteis" rotulados como resposta e resolução. **Não** atribui valores de incidente crítico a chamados gerais. |
| **VC-23** *(multi-domínio + armadilha de inversão)* | "Cliente quer devolver uma carga perigosa de 700kg que recebeu há 3 dias. Está no prazo? Como fica o frete?" | Primeiro: carga perigosa **não é elegível para devolução pelo processo padrão**, com encaminhamento à Gestão de Riscos (ramal 4500). O prazo de 7 dias úteis é a regra geral e não torna a carga elegível. Não calcula frete reverso como se a devolução padrão fosse possível. | Cita `POL-001 §3.2` e `POL-001 §3.1`. Contém "não elegível" / "processo padrão" e "4500". **Não** afirma que a devolução pode seguir o procedimento padrão. **Não** aplica multiplicadores de frete reverso. |

### 5.2 Rastreabilidade VC → chunks do Anexo B

Cada VC vira uma entrada em `prompts/eval/golden-queries.json` e nas fixtures `tests/fixtures/queries.ts`, `chunks.ts` e `expected-responses.ts` (Anexo C). A tabela abaixo mostra se os chunks do Anexo B sustentam o resultado esperado.

| VC | Chunks necessários (Anexo B) | Cobertura | Observação |
|---|---|---|---|
| VC-01 | POL-001-B (+ FAQ-03) | ✅ Total | — |
| VC-02 | SLA-2024-A (+ FAQ-15) | ⚠️ Parcial | O encaminhamento ao Comercial (SLA-2024 §1 Nota) não está no chunk |
| VC-03 | PROC-042-A, B, C; PROC-042v2-A, B, C, E | ⚠️ Parcial | 7 chunks, acima do limite de 5 (T-06). O gabarito exige só a v2 (T-05) |
| VC-04 | Nenhum | ✅ Coerente | O gabarito confirma: não há chunk para carga abaixo de 500kg |
| VC-05 | POL-001-A, D; PROC-042-B; PROC-042v2-B | ✅ Total | 4 chunks, dentro do limite |
| VC-06 | SLA-2024-C | ⚠️ Parcial | "O relógio não pausa" (SLA-2024 §5) não tem chunk |
| VC-07 | SLA-2024-B | ⚠️ Parcial | A pausa fora de 08h-18h (§5) não tem chunk |
| VC-08 | SLA-2024-C, D | ✅ Total | O SLA-2024-D diz "valor acima de", sem "declarado" |
| VC-09 | SLA-2024-D | ✅ Total | O FAQ Item 27 não tem chunk, e isso não afeta o resultado |
| VC-10 | POL-001-D; FAQ-38 | ✅ Total | — |
| VC-11 | Nenhum (o FAQ Item 22 não tem chunk) | ❌ Sem cobertura | Com o Anexo B atual, o resultado correto é "não encontrado" |
| VC-12 | PROC-042v2-D | ⚠️ Parcial | O desconto da v1 (§4) e o FAQ Item 45 não têm chunk |
| VC-13 | Nenhum | ❌ Sem cobertura | O escopo da POL-001 §2 (PROC-088) não tem chunk |
| VC-14 | Nenhum | ❌ Sem cobertura | A cadeia de frio (POL-001 §3.2) não aparece no POL-001-B |
| VC-15 | POL-001-A | ✅ Total | — |
| VC-16 | PROC-042-B / PROC-042v2-B | ✅ Total | Testa a janela de histórico, não a recuperação |
| VC-17 | Nenhum | ✅ Coerente | Fora de escopo |
| VC-18 | PROC-042-A; PROC-042v2-A | ✅ Total | Os chunks preservam a ambiguidade ("acima de 500kg" × "500-1.000kg") |
| VC-19 | — | — | Latência |
| VC-20 | FAQ-32 | ✅ Total | — |
| VC-21 | PROC-042-B; PROC-042v2-B | ✅ Total | Sujeito à T-05 |
| VC-22 | SLA-2024-B (+ SLA-2024-A, C) | ✅ Total | — |
| VC-23 | POL-001-A, B (+ PROC-042v2-A, B) | ✅ Total | É o exemplo multi-domínio do gabarito |

**Resumo:** 3 VCs sem cobertura (VC-11, VC-13, VC-14) e 5 com cobertura parcial (VC-02, VC-03, VC-06, VC-07, VC-12). A solução é decidir entre regenerar os chunks e ajustar o resultado esperado para "não encontrado" (OQ-19).

---

## 6. Open Questions

- **OQ-01 (T-01).** Diante de uma contradição de versões, o assistente mostra as duas (spec) ou prioriza a mais recente (ADR-0003)? Se for as duas, a ADR-0003 precisa ser revisada.
- **OQ-02 (T-02).** Qual valor de vigência o pipeline grava para a PROC-042 v1 e a PROC-042-v2, se nenhuma delas tem indicação formal?
- **OQ-03 (T-03).** Os 5 chunks da ADR-0002 bastam para pergunta multi-domínio com contradição de versões? Existe prioridade de seleção (ex.: garantir um chunk de cada versão)?
- **OQ-04 (T-04).** O que o assistente faz quando a pergunta depende de um turno fora da janela de 3?
- **OQ-05.** Como mapear cidade ou UF para região (Manaus, Salvador)? O modelo pode usar conhecimento geográfico geral?
- **OQ-06.** Carga de exatamente 500kg é frete especial?
- **OQ-07.** A regra de transição do v2 §5 vale só para multiplicadores ou também para fator de peso, prazo e desconto?
- **OQ-08.** Conteúdo que só existe no FAQ deve ser exibido com rótulo ou omitido?
- **OQ-09.** Para incidente crítico de Silver e Standard, o relógio pausa?
- **OQ-10.** O assistente deve calcular datas (ex.: "até segunda 11h") ou só informar a regra?
- **OQ-11.** Qual é o limite de confiança ou a regra de "não sei" (quando a recuperação é fraca)? **[A DEFINIR — validar com TL/NovaTech]**
- **OQ-12.** Qual é a política para perguntas fora das 4 categorias?
- **OQ-13.** A spec anterior não cita "prazos de entrega", que é uma categoria do discovery. Confirmar se está no escopo.
- **OQ-14.** Quais são os 12 documentos com contradição pendente, e qual é o formato do sinal na resposta (C-14)?
- **OQ-15.** Os 30 s valem até o endpoint devolver a resposta ou até ela aparecer no Teams ou no painel?
- **OQ-16.** Avaria em trânsito segue a POL-001 §3.5 ou o processo de sinistro do FAQ Item 38?
- **OQ-17 (T-05).** O gabarito do Anexo B, que espera só a v2, é o critério oficial de retrieval? Se for, a spec ("mostrar ambas") precisa ser revista.
- **OQ-18 (T-07).** Os 63 documentos "descartados por obsolescência" continuam no índice, marcados como obsoletos, ou foram removidos?
- **OQ-19.** Para VC-11, VC-13 e VC-14 (sem chunk), qual é o caminho: o pipeline regenera os chunks para cobrir a regra, ou o resultado esperado passa a ser "não encontrado"?
- **OQ-20.** Qual é o formato das entradas de `prompts/eval/golden-queries.json` e quem mantém a correspondência VC → golden query?
- **OQ-21.** O formato de citação usa a seção do documento (`POL-001 §3.2`) ou o ID do chunk (`POL-001-B`)?
