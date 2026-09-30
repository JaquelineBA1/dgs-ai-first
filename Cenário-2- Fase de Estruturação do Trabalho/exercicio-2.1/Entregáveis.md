**Tarefa:**
1. Usando o **Claude**, faça o recorte de domínio do projeto:
   - Identifique os bounded contexts do assistente NovaTech (ex: "Atendimento ao Cliente", "Gestão Documental", "SLAs e Contratos", "Logística de Frete"). Para cada contexto, defina: o que está dentro, o que está fora, e como se relaciona com os outros.
   - Extraia a linguagem ubíqua do domínio a partir do Anexo A: termos que precisam ser usados de forma consistente por humanos e agentes (ex: "carga perigosa" sempre significa "classes 1-6 da ANTT", "frete especial" sempre significa "acima de 500kg").

# NovaTech Assistant — Recorte de Domínio

> Bounded contexts e linguagem ubíqua do NovaTech Assistant. Referência para o time e para os agentes de IA (Copilot, Claude Code).

| Campo | Valor |
|---|---|
| **Fonte de verdade** | Anexo A (POL-001, PROC-042, PROC-042-v2, SLA-2024, FAQ-Atendimento) |
| **Apoio** | Anexo B (chunks de referência do pipeline de RAG) |
| **Convenção de citação** | `DOC §seção` (ex.: `POL-001 §3.2`). O FAQ é citado por item (`FAQ Item 15`). |
| **Exercício** | 2.1 — Recorte de domínio e spec SDD (Tarefa 1) |
| **Caminho sugerido** | `docs/domain/bounded-contexts-e-linguagem-ubiqua.md` |

## Sumário

- [1. Bounded contexts](#1-bounded-contexts)
  - [1.1 Decisão sobre os pontos de partida](#11-decisão-sobre-os-pontos-de-partida)
  - [1.2 Atendimento ao Cliente](#12-contexto-a--atendimento-ao-cliente)
  - [1.3 Devoluções](#13-contexto-b--devoluções)
  - [1.4 Frete e Prazos de Entrega](#14-contexto-c--frete-e-prazos-de-entrega)
  - [1.5 SLAs e Contratos](#15-contexto-d--slas-e-contratos)
  - [1.6 Gestão Documental](#16-contexto-e--gestão-documental)
  - [1.7 Mapa de relacionamento](#17-mapa-de-relacionamento)
  - [1.8 Fronteira do assistente](#18-fronteira-do-assistente-o-que-ele-faz-e-o-que-não-faz)
  - [1.9 Perguntas que cruzam categorias](#19-perguntas-que-cruzam-categorias-os-15-do-discovery)
- [2. Linguagem ubíqua — Glossário](#2-linguagem-ubíqua--glossário)
- [3. Termos sem definição na base](#3-termos-sem-definição-na-base)
- [4. Contradições adicionais encontradas](#4-contradições-adicionais-encontradas)
- [5. Cobertura da linguagem ubíqua nos chunks (Anexo B)](#5-cobertura-da-linguagem-ubíqua-nos-chunks-anexo-b)
- [6. Perguntas em aberto para validar com a NovaTech](#6-perguntas-em-aberto-para-validar-com-a-novatech)

---

## 1. Bounded contexts

### 1.1 Decisão sobre os pontos de partida

| Ponto de partida sugerido | Decisão | Justificativa no Anexo A |
|---|---|---|
| Atendimento ao Cliente | **Confirmado** | É onde o atendente atua. O FAQ inteiro é escrito do ponto de vista dele ("O que respondo?", "O que faço?"). |
| Gestão Documental | **Confirmado (contexto de suporte)** | Cada documento tem Versão, Status/Classificação e Responsável próprios. A coexistência de PROC-042 v1 e v2 "sem hierarquia clara" (PROC-042-v2, cabeçalho) é um problema de governança documental, não de frete. |
| SLAs e Contratos | **Confirmado** | O SLA-2024 é o único "Documento contratual" da base (cabeçalho) e define tiers, SLAs de atendimento, incidente crítico e penalidades. |
| Logística de Frete | **Ajustado → "Frete e Prazos de Entrega"** | O único conteúdo de frete da base é o **frete especial** (PROC-042 §1). O único prazo de entrega documentado está no PROC-042 §3 (prazo padrão da rota + dias úteis). A categoria "prazos de entrega" do discovery cai aqui. |
| *(não sugerido)* | **Novo → "Devoluções"** | O POL-001 tem responsável próprio (Diretoria de Operações, diferente da Diretoria Comercial do PROC-042), seus próprios prazos (7 dias úteis, 4 horas úteis de triagem, 2 e 5 dias úteis) e regras de elegibilidade. "Política de devolução" também é uma das 4 categorias do discovery. Deixar isso dentro de "Atendimento" misturaria regra de negócio com orientação ao atendente. |

Também existem **contextos externos**. A base cita esses contextos, mas não os documenta: Gestão de Riscos, Comercial, Jurídico/Sinistros e Compliance, além dos documentos PROC-088 e PROC-043, que não estão na base. O assistente só pode **encaminhar** para eles, nunca responder por eles.

---

### 1.2 Contexto A — Atendimento ao Cliente

> **Tipo:** core (é o contexto do assistente)

**DENTRO**
- Orientação prática ao atendente: o que responder e para onde encaminhar (FAQ Itens 3, 15, 27, 38, 45).
- Identificação do cliente pelo atendente, com o pedido do número do contrato (FAQ Item 15).
- Pergunta sobre rastreamento e a abertura de "chamado de rastreamento" (FAQ Item 27). Isso é informal.
- Limites de autonomia do atendente: "Atendente não tem autonomia para dar desconto" (FAQ Item 45). Isso também é informal.
- Encaminhamentos documentados: Gestão de Riscos ramal 4500 (POL-001 §3.2), Comercial (POL-001 §3.5; SLA-2024 §1 Nota), sinistros@novatech.com.br (FAQ Item 38, informal).
- Documento de origem: **FAQ-Atendimento** (classificação: "Documento informal — NÃO validado").

**FORA**
- As regras em si: elegibilidade de devolução (→ Devoluções), fórmula de frete (→ Frete e Prazos), valores de SLA (→ SLAs e Contratos).
- A validade de cada documento (→ Gestão Documental).
- A decisão de exceções (→ Gestão de Riscos, Comercial ou Jurídico, que são externos).

**Relações e mudanças de significado**
- Consome os três contextos de regra de negócio e o metadado de classificação da Gestão Documental.
- **"Chamado"** aqui é a interação do atendente com o cliente. Em SLAs e Contratos, é a unidade de medição com timestamp no Azure DevOps (SLA-2024 §5). Em Devoluções, é a solicitação que o *cliente* abre no Portal do Cliente (POL-001 §3.3). Em Frete, a data de abertura do chamado decide qual versão da PROC-042 vale (PROC-042-v2 §5).
- **"Prioridade alta"** (FAQ Item 27) só existe aqui e **não** é "incidente crítico" (SLA-2024 §3).

**Categorias do discovery:** todas as 4, como ponto de entrada. Ele não é dono das regras de nenhuma delas.

---

### 1.3 Contexto B — Devoluções

**DENTRO** (POL-001 inteiro)
- Prazo geral de 7 dias úteis após o recebimento confirmado no tracking, com a definição de dias úteis (§3.1).
- Exceções: carga perigosa (classes 1 a 6 da ANTT), carga refrigerada com cadeia de frio rompida e lacre violado (§3.2).
- Procedimento: Portal do Cliente, CT-e, 3 fotos, triagem em 4 horas úteis, coleta reversa agendada em até 2 dias úteis, reembolso ou crédito em até 5 dias úteis (§3.3).
- Devolução parcial (§3.4) e custos por motivo (§3.5).
- **Dono da definição de "carga perigosa"**, que é compartilhada com os outros contextos (*shared kernel*).

**FORA**
- Mercadoria ainda em trânsito (→ PROC-088, externo e fora da base; POL-001 §2).
- Tratamento individual das exceções (→ Gestão de Riscos, externo).
- Negociação de prazo expirado (→ Comercial, externo).
- Cálculo dos multiplicadores do frete reverso (→ Frete e Prazos).
- Carga danificada em trânsito como "sinistro" (→ Jurídico, externo; só aparece no FAQ Item 38).

**Relações e mudanças de significado**
- **Consome Frete e Prazos:** o frete reverso por desistência é "calculado com os mesmos multiplicadores do frete original" (POL-001 §3.5).
- **Fornece "carga perigosa"** para Frete (PROC-042 §4) e para SLAs (SLA-2024 §3, incidente crítico).
- **"Prazo"** aqui é o limite para o *cliente pedir* a devolução (dias úteis). Em Frete, é prazo de entrega. Em SLAs, é tempo de resposta ou resolução.
- **"Processo padrão"** aqui é o fluxo do §3.3. Não tem relação com o tier "Standard" nem com um "frete padrão".
- **Conflito de fronteira:** POL-001 §3.5 trata "avaria em trânsito" *dentro* da devolução, sem custo para o cliente. O FAQ Item 38 diz que carga danificada "tem processo diferente de devolução".

**Categorias do discovery:** política de devolução.

---

### 1.4 Contexto C — Frete e Prazos de Entrega

**DENTRO** (PROC-042 v1 e PROC-042-v2)
- Definição de frete especial para carga acima de 500kg (§1).
- Fórmula: Valor base × Multiplicador regional × Fator de peso (§2).
- Multiplicadores regionais (§2.1), **diferentes entre v1 e v2**.
- Fator de peso por faixa, **diferente entre v1 e v2**.
- Prazo de entrega do frete especial: prazo padrão da rota + 2 dias úteis (v1) ou + 3 dias úteis (v2) (§3).
- Condições especiais: aprovação para carga acima de 5.000kg, carga perigosa acima de 500kg vai para a PROC-043 e descontos de volume (§4).
- Regra de transição por data de abertura do chamado (v2 §5).

**FORA**
- Frete abaixo de 500kg: **não existe documento na base** (lacuna listada nas Notas do Anexo A, item 3).
- Frete de carga perigosa acima de 500kg (→ PROC-043, fora da base e "em processo de revisão pelo Compliance", segundo o v2 §4).
- Conteúdo da tabela mensal de valor base (arquivo `frete-base-AAAAMM.xlsx`, fora da base).
- Qual versão está vigente (→ Gestão Documental e Compliance).
- Negociação de desconto (→ Comercial, externo).

**Relações e mudanças de significado**
- É consumido por Devoluções (frete reverso) e por SLAs e Contratos (a penalidade é "crédito de 5% sobre o valor do frete", SLA-2024 §4).
- Consome "carga perigosa" de Devoluções.
- **Depende fortemente de Gestão Documental**, porque é o único contexto com duas versões coexistindo.
- **"Desconto"** muda de significado entre as versões: é negociado pelo Comercial com aditivo no v1 §4, é um percentual sobre o multiplicador no v2 §4 e é "automático" no FAQ Item 45.

**Categorias do discovery:** regras de frete e prazos de entrega.

---

### 1.5 Contexto D — SLAs e Contratos

**DENTRO** (SLA-2024 inteiro)
- Tiers Gold, Silver e Standard com critérios de elegibilidade e revisão. "Não existem outros tiers" (§1).
- Tabela de SLAs: primeira resposta e resolução para chamados gerais e para incidentes críticos, disponibilidade do portal de tracking, gerente de conta e relatório mensal (§2).
- Definição de incidente crítico (§3), penalidades (§4) e medição com pausa de relógio (§5).

**FORA**
- Prazos de entrega de carga (→ Frete e Prazos). **SLA aqui é de atendimento, não de entrega.**
- Negociação de SLA diferenciado (→ Comercial, externo; §1 Nota).
- Consulta ao tier real de um cliente específico (não há fonte na base).

**Relações e mudanças de significado**
- É consumido por Atendimento (tier e prazos de resposta).
- Consome "carga perigosa" de Devoluções (§3) e "valor do frete" de Frete (§4).
- **"Resposta" e "resolução"** são métricas diferentes. A definição de cada uma só existe no FAQ Item 41.
- **"Horas úteis"** (chamados gerais) e **"horas"** (incidentes críticos: "Até 30min", "Até 4h") têm unidades diferentes. O relógio "não pausa para incidentes críticos de clientes Gold" (§5).
- **Triagem de devolução** (4 horas úteis, POL-001 §3.3) **não é** "tempo de primeira resposta" (SLA-2024 §2).

**Categorias do discovery:** SLAs.

---

### 1.6 Contexto E — Gestão Documental

> **Tipo:** suporte

**DENTRO**
- Metadados de cada documento: Versão, data de emissão/atualização, Responsável e Classificação ("Documento normativo", "Documento contratual", "Documento informal — NÃO validado").
- Status de coexistência de versões (PROC-042 e PROC-042-v2, cabeçalhos).
- A base consolidada: **847 documentos válidos, 63 descartados por obsolescência e 12 com contradições pendentes** de resolução pelo Compliance (cenário, fase anterior).
- ⚠️ **Tensão de vocabulário:** a ADR-0003 diz que "documentos obsoletos [são] marcados, não excluídos", mas o cenário fala em 63 documentos "descartados por obsolescência". *Descartado* e *obsoleto marcado* não podem significar a mesma coisa sem uma definição (ver seção 3 e pergunta 17).
- As contradições e lacunas registradas nas Notas do Anexo A.

**FORA**
- O conteúdo das regras (→ contextos B, C e D).
- A **resolução** das contradições (→ Compliance, externo).

**Relações e mudanças de significado**
- É *upstream* de todos os outros: fornece vigência, classificação e marcação de contradição.
- **"Versão"** tem significados diferentes conforme o documento. O FAQ tem "Versão: Não controlada". O PROC-042 tem versão, mas "não possui indicação formal de vigência ou obsolescência".
- **"Válido"** (os 847 documentos válidos) não é o mesmo que **"vigente"**: o PROC-042 v1 é válido na base, mas a vigência dele é indefinida.

**Categorias do discovery:** nenhuma diretamente. Ele condiciona as 4.

---

### 1.7 Mapa de relacionamento

```mermaid
flowchart LR
    GD["Gestão Documental<br/>(suporte)"]
    AT["Atendimento ao Cliente<br/>(core)"]
    DV["Devoluções"]
    FR["Frete e Prazos de Entrega"]
    SL["SLAs e Contratos"]
    subgraph EXT["Contextos externos (fora da base)"]
        GR["Gestão de Riscos<br/>ramal 4500"]
        CO["Comercial"]
        JU["Jurídico / Sinistros"]
        CP["Compliance"]
        P88["PROC-088"]
        P43["PROC-043"]
    end

    GD -->|vigência / classificação| AT
    GD -->|vigência / versão| DV
    GD -->|"vigência / versão (v1 × v2)"| FR
    GD -->|vigência / versão| SL
    AT -->|consome| DV
    AT -->|consome| FR
    AT -->|consome| SL
    DV -->|multiplicadores do frete reverso| FR
    SL -->|valor do frete p/ penalidade| FR
    DV -.->|"shared kernel: carga perigosa"| FR
    DV -.->|"shared kernel: carga perigosa"| SL
    DV -->|encaminha| GR
    DV -->|encaminha| CO
    DV -->|encaminha| P88
    FR -->|encaminha| P43
    FR -->|encaminha| CO
    SL -->|SLA diferenciado| CO
    AT -.->|informal, FAQ 38| JU
    CP -->|resolve contradições| GD
```

<details>
<summary>Versão em texto do mapa</summary>

```
Gestão Documental ──fornece vigência/classificação/contradição──▶ Atendimento ao Cliente
Gestão Documental ──fornece vigência/versão──▶ Frete e Prazos de Entrega   (PROC-042 v1 × v2)
Gestão Documental ──fornece vigência/versão──▶ Devoluções
Gestão Documental ──fornece vigência/versão──▶ SLAs e Contratos

Atendimento ao Cliente ──consome──▶ Devoluções
Atendimento ao Cliente ──consome──▶ Frete e Prazos de Entrega
Atendimento ao Cliente ──consome──▶ SLAs e Contratos

Devoluções ──consome (multiplicadores do frete reverso)──▶ Frete e Prazos de Entrega
SLAs e Contratos ──consome (valor do frete p/ penalidade)──▶ Frete e Prazos de Entrega

Devoluções ══shared kernel "carga perigosa (classes 1 a 6 ANTT)"══▶ Frete e Prazos de Entrega (PROC-042 §4)
Devoluções ══shared kernel "carga perigosa"══▶ SLAs e Contratos (SLA-2024 §3)

Devoluções ──encaminha──▶ [ext] Gestão de Riscos (ramal 4500) | [ext] Comercial | [ext] PROC-088
Frete e Prazos ──encaminha──▶ [ext] PROC-043 | [ext] Comercial | [ext] Gerente de operações regional
SLAs e Contratos ──encaminha──▶ [ext] Comercial (SLA diferenciado)
Atendimento ──encaminha (informal)──▶ [ext] Jurídico/sinistros@ (FAQ Item 38)
[ext] Compliance ──resolve contradições──▶ Gestão Documental
```

</details>

**Onde os termos mudam de significado (resumo):**

| Termo | Devoluções | Frete e Prazos | SLAs e Contratos | Atendimento |
|---|---|---|---|---|
| Prazo | limite para pedir devolução (7 dias úteis) | prazo de entrega (rota + 2 ou + 3 dias úteis) | tempo de resposta/resolução (horas) | — |
| Chamado | solicitação do cliente no Portal | data de abertura define a versão (v2 §5) | unidade medida no Azure DevOps | interação com o cliente |
| Padrão / Standard | "processo padrão" de devolução | "prazo padrão da rota" (sem definição) | tier "Standard" | — |
| Desconto | — | negociado (v1) × % sobre multiplicador (v2) | crédito por violação (§4) | "automático" (FAQ 45) |

---

### 1.8 Fronteira do assistente: o que ele faz e o que não faz

Esta é a fronteira do **assistente como produto**. As fronteiras por módulo ficam no [`specs/query-endpoint/requirements.md`](../../specs/query-endpoint/requirements.md).

| O assistente FAZ | Base |
|---|---|
| Responde a **atendentes** da NovaTech sobre as 4 categorias do discovery (prazos de entrega, regras de frete, política de devolução, SLAs) | Cenário; discovery |
| Responde **somente** com base nos documentos da base, e toda resposta cita a fonte | Spec de RAG anterior |
| Mostra as duas versões quando as fontes se contradizem (ex.: PROC-042 v1 × v2) | Spec de RAG anterior |
| Diferencia regra normativa ou contratual de prática informal (FAQ) | Classificação nos cabeçalhos do Anexo A |
| Diz explicitamente quando a base não cobre o assunto (ex.: frete abaixo de 500kg) | Notas do Anexo A, lacuna 3 |
| Indica os encaminhamentos que os documentos preveem (Gestão de Riscos ramal 4500, Comercial) | POL-001 §3.2 e §3.5; SLA-2024 §1 Nota |

| O assistente NÃO FAZ | Quem faz / por quê |
|---|---|
| Atender o cliente final diretamente | O usuário é o atendente (cenário) |
| Decidir exceções de devolução, descontos ou SLA diferenciado | Gestão de Riscos, Comercial ou Diretoria Comercial (POL-001 §3.2 e §3.5; PROC-042-v2 §4; SLA-2024 §1 Nota) |
| Decidir qual versão de documento está vigente | Compliance / Gestão Documental |
| Calcular valor de frete em R$ | O *valor base* está numa tabela fora da base (PROC-042 §2) |
| Descrever o conteúdo de documentos que não estão na base (PROC-088, PROC-043) | Só cita que eles existem (POL-001 §2; PROC-042 §4) |
| Executar ações em sistemas (Portal do Cliente, tracking, Azure DevOps) | A arquitetura não prevê essas integrações (cenário: 3 componentes) |
| Descobrir o tier de um cliente específico | Não há fonte de dados de cliente na base |
| Ingerir, atualizar ou marcar documentos | Pipeline de ingestão |

### 1.9 Perguntas que cruzam categorias (os 15% do discovery)

O discovery diz que 15% das perguntas cruzam duas categorias, mas não diz **quais pares**. O Anexo A mostra onde as regras de fato se tocam:

| Cruzamento | Ponto de contato no Anexo A | Contextos envolvidos |
|---|---|---|
| Devolução × Frete | O frete reverso por desistência é "calculado com os mesmos multiplicadores do frete original" (POL-001 §3.5 → PROC-042 §2.1) | DV → FR (com contradição v1 × v2) |
| Devolução × Frete (carga perigosa) | Carga perigosa não é elegível para devolução padrão (POL-001 §3.2) e, acima de 500kg, segue a PROC-043 (PROC-042 §4) | DV ═ FR (shared kernel) |
| Devolução × SLA | Triagem de 4 horas úteis (POL-001 §3.3) × primeira resposta por tier (SLA-2024 §2) | DV × SL (termos parecidos, métricas diferentes) |
| SLA × Frete | A penalidade é um crédito sobre "o valor do frete do chamado afetado" (SLA-2024 §4) | SL → FR |
| SLA × Devolução (carga perigosa) | "Carga perigosa com qualquer irregularidade" é critério de incidente crítico (SLA-2024 §3) | SL ═ DV (shared kernel) |
| Frete × Prazos de entrega | Os dois estão na PROC-042 (§2 e §3) | Mesmo contexto (FR) |

**Observação:** "Frete × Prazos de entrega" cruza duas *categorias* do discovery, mas fica dentro de *um só contexto*. Categoria de pergunta e bounded context não são a mesma coisa. O exemplo multi-domínio do Anexo B ("Prazo de devolução + carga perigosa + frete especial") envolve DV e FR.

---

## 2. Linguagem ubíqua — Glossário

Legenda de contexto: **AT** Atendimento · **DV** Devoluções · **FR** Frete e Prazos · **SL** SLAs e Contratos · **GD** Gestão Documental.
⚠️ **SRN** = sem respaldo em documento normativo (aparece só no FAQ informal).

### 2.1 Tiers e classificação de cliente

| Termo | Definição (exatamente como no documento) | Origem | BC | Como um LLM poderia interpretar errado |
|---|---|---|---|---|
| Gold | "Contrato anual acima de R$ 500.000 OU mais de 200 operações/mês" — Revisão: "Semestral" | SLA-2024 §1 | SL | Pode tratar como o metal, uma cor ou um cartão de crédito. Pode ler o "OU" como "E". Pode atribuir benefícios de programas de fidelidade genéricos. |
| Silver | "Contrato anual entre R$ 100.000 e R$ 500.000 OU entre 50 e 200 operações/mês" — Revisão: "Semestral" | SLA-2024 §1 | SL | Pode tratar como a prata. Pode classificar como Gold um contrato de exatamente R$ 500.000 (o documento diz "acima de" para Gold). |
| Standard | "Todos os demais clientes" — Revisão: "Anual" | SLA-2024 §1 | SL | Pode confundir com "processo padrão" (POL-001) ou com um "frete padrão", que não está documentado. Pode achar que é um tier "sem SLA". |
| Tiers (quantidade) | "Não existem outros tiers além dos três listados acima." | SLA-2024 §1 Nota | SL | Pode inferir Platinum, Bronze ou Diamond por analogia com outras empresas. |
| Platinum | "Não existe tier Platinum na NovaTech." | FAQ Item 15 (inexistência respaldada por SLA-2024 §1 Nota) | AT | Pode responder com SLAs "superiores ao Gold" por extrapolação. |
| Programa de fidelidade antigo | "programa de fidelidade antigo que foi descontinuado em 2022" | FAQ Item 15 — ⚠️ SRN | AT | Pode tratar como ativo ou atribuir benefícios a ele. |
| Gerente de conta dedicado | Gold: "Sim"; Silver: "Não"; Standard: "Não" | SLA-2024 §2 | SL | Pode supor que Silver também tem. Pode confundir com "gerente de operações" (SLA-2024 §4) ou com o "gerente de operações regional" (PROC-042 §4). |

### 2.2 SLAs: pares que costumam ser confundidos e unidades

| Termo | Definição (exatamente como no documento) | Origem | BC | Como um LLM poderia interpretar errado |
|---|---|---|---|---|
| SLA | "os SLAs listados aqui são compromissos formais com o cliente" | SLA-2024, cabeçalho (Classificação) | SL | Pode tratar SLA como **prazo de entrega da carga**. Na base, SLA é tempo de atendimento de chamados e disponibilidade do portal. |
| Tempo de primeira resposta (chamados gerais) | "Até 2h úteis" (Gold) / "Até 4h úteis" (Silver) / "Até 8h úteis" (Standard) | SLA-2024 §2 | SL | Pode tirar o "úteis" e calcular em horas corridas. Pode confundir com resolução. |
| Tempo de resolução (chamados gerais) | "Até 24h úteis" / "Até 48h úteis" / "Até 72h úteis" | SLA-2024 §2 | SL | Pode converter "24h úteis" em "1 dia" ou "72h" em "3 dias corridos". |
| Resposta (definição) | "Resposta é quando a gente dá o primeiro retorno ao cliente (mesmo que seja 'estamos verificando')." | FAQ Item 41 — ⚠️ SRN (os valores coincidem com SLA-2024 §2, mas a *definição* só existe no FAQ) | AT/SL | Pode exigir uma resposta com solução para considerar o SLA de resposta cumprido. |
| Resolução (definição) | "Resolução é quando o problema é efetivamente resolvido." | FAQ Item 41 — ⚠️ SRN | AT/SL | Pode considerar o primeiro retorno como resolução. |
| Tempo de primeira resposta (incidentes críticos) | "Até 30min" / "Até 1h" / "Até 2h" | SLA-2024 §2 | SL | Pode aplicar "úteis" por analogia com os chamados gerais. Na tabela, a palavra não aparece. |
| Tempo de resolução (incidentes críticos) | "Até 4h" / "Até 8h" / "Até 24h" | SLA-2024 §2 | SL | Pode confundir "24h" (Standard crítico, horas) com "24h úteis" (Gold geral). |
| Chamado geral | Termo usado nas linhas da tabela ("chamados gerais"). **Não há definição explícita.** | SLA-2024 §2 | SL | Pode incluir incidentes críticos nessa categoria. |
| Incidente crítico | "quando atende a pelo menos um dos seguintes critérios: Carga com valor declarado acima de R$ 100.000 está com status desconhecido há mais de 6 horas. / Carga perigosa com qualquer irregularidade de documentação ou rastreamento. / Mais de 5 chamados do mesmo cliente nas últimas 24 horas sobre o mesmo problema. / Qualquer situação que envolva risco à segurança de pessoas." | SLA-2024 §3 | SL | Pode classificar por "gravidade percebida" em vez dos critérios. Pode exigir todos os critérios quando basta "pelo menos um". Pode aplicar o limite de R$ 50.000 do FAQ Item 27. |
| Horário comercial / pausa do relógio | "O relógio de SLA pausa fora do horário comercial (08h-18h, dias úteis) para chamados gerais, mas **não pausa** para incidentes críticos de clientes Gold." | SLA-2024 §5 | SL | Pode concluir que o relógio não pausa para incidentes críticos de *todos* os tiers. O texto só garante isso para Gold. |
| Violação de SLA / penalidade | "Primeira violação de SLA no mês: registro interno, sem impacto contratual. / Segunda violação no mesmo mês: crédito de 5% sobre o valor do frete do chamado afetado. / Terceira violação ou mais no mesmo mês: crédito de 10% + reunião obrigatória com o gerente de conta (Gold) ou gerente de operações (Silver/Standard)." | SLA-2024 §4 | SL | Pode tratar o crédito como desconto de frete (PROC-042). Pode aplicar a porcentagem sobre o contrato inteiro em vez do frete do chamado afetado. |
| Disponibilidade do portal de tracking | "99,5%" / "99,0%" / "98,0%" | SLA-2024 §2 | SL | Pode interpretar como taxa de entregas no prazo. |
| Prioridade alta | "classifique como prioridade alta se for Gold ou se o valor da carga for acima de R$ 50.000" | FAQ Item 27 — ⚠️ SRN | AT | Pode tratar como sinônimo de incidente crítico, que usa R$ 100.000 e mais de 6 horas. |

### 2.3 Devoluções

| Termo | Definição (exatamente como no documento) | Origem | BC | Como um LLM poderia interpretar errado |
|---|---|---|---|---|
| Prazo geral de devolução | "O cliente pode solicitar a devolução de mercadorias em até **7 (sete) dias úteis** após a data de recebimento confirmada no sistema de tracking." | POL-001 §3.1 | DV | Pode usar 7 dias corridos (como no direito de arrependimento do consumidor). Pode contar a partir da compra ou da emissão. Pode confundir "solicitar" com "concluir" a devolução. |
| Dias úteis | "A contagem de dias úteis exclui sábados, domingos e feriados nacionais." | POL-001 §3.1 | DV (usado por FR e SL sem redefinição) | Pode excluir também feriados estaduais ou municipais. Pode supor que a mesma regra vale para SLA-2024 e PROC-042, que não redefinem o termo. |
| Carga perigosa | "classificadas nas classes 1 a 6 da ANTT (Agência Nacional de Transportes Terrestres), conforme Resolução ANTT nº 5.947/2021. Inclui: explosivos (classe 1), gases (classe 2), líquidos inflamáveis (classe 3), sólidos inflamáveis (classe 4), oxidantes e peróxidos (classe 5), substâncias tóxicas e infectantes (classe 6)." | POL-001 §3.2 | DV (shared kernel) | Pode usar o conceito geral de "perigoso" (frágil, cara, com baterias) ou classes além de 1 a 6. Pode transformar "não elegível pelo processo padrão" em "proibido devolver". |
| Não elegível pelo processo padrão | "As seguintes categorias de carga **NÃO são elegíveis** para devolução pelo processo padrão" … "o cliente deve entrar em contato com o setor de **Gestão de Riscos** (ramal 4500) para tratamento individual." | POL-001 §3.2 | DV | Pode responder "não pode devolver" (o FAQ Item 3 alerta: "não diga que é impossível") ou, no extremo oposto, "pode, com exceção". |
| Exceção autorizada por Riscos | "já tiveram casos em que o pessoal de Riscos autorizou exceção" | FAQ Item 3 — ⚠️ SRN | AT | Pode prometer uma exceção ao cliente. |
| Cadeia de frio rompida | "Cargas refrigeradas que tenham rompido a cadeia de frio (temperatura fora da faixa especificada na nota fiscal por mais de 30 minutos contínuos, conforme registro do sensor IoT)." | POL-001 §3.2 | DV | Pode somar intervalos que não são contínuos. Pode considerar qualquer oscilação. Pode ignorar que a faixa vem da nota fiscal. |
| Lacre de segurança violado | "Cargas com lacre de segurança violado, salvo quando a violação for documentada no ato de entrega com assinatura do motorista e do recebedor." | POL-001 §3.2 | DV | Pode ignorar a exceção e aceitar só uma das duas assinaturas. |
| Triagem | "O time de atendimento tem **4 horas úteis** para triagem do chamado (verificar elegibilidade, documentação e prazo)." | POL-001 §3.3 item 3 | DV | Pode confundir com o "tempo de primeira resposta" do SLA-2024, que é de 2, 4 ou 8h úteis por tier. |
| Coleta reversa | "Se elegível, a coleta reversa é agendada em até **2 dias úteis** após aprovação." | POL-001 §3.3 item 4 | DV | Pode dizer que a coleta é *feita* em 2 dias. O texto diz *agendada*. |
| Reembolso ou crédito | "O reembolso ou crédito é processado em até **5 dias úteis** após o recebimento da mercadoria devolvida no centro de distribuição." | POL-001 §3.3 item 5 | DV | Pode contar a partir da solicitação ou da aprovação. Pode confundir "processado" com "creditado na conta". |
| CT-e | "número do CT-e (Conhecimento de Transporte Eletrônico)" | POL-001 §3.3 item 2 | DV | Pode confundir com NF-e ou pedir a nota fiscal no lugar. |
| Fotos obrigatórias | "fotos da mercadoria no estado atual (mínimo 3 fotos: embalagem externa, etiqueta de identificação, e conteúdo)" | POL-001 §3.3 item 2 | DV | Pode aceitar "algumas fotos" sem os três ângulos exigidos. |
| Portal do Cliente | "O cliente abre chamado no **Portal do Cliente** (portal.novatech.com.br), selecionando a categoria 'Devolução de Mercadoria'." | POL-001 §3.3 item 1 | DV | Pode dizer que o atendente abre o chamado. Pode confundir com o "portal de tracking" (SLA-2024 §2). |
| Devolução parcial | "Quando a entrega envolver múltiplos volumes, o cliente pode devolver volumes individuais. […] O cálculo de reembolso é proporcional ao peso/valor do volume devolvido, conforme o CT-e." | POL-001 §3.4 | DV | Pode escolher peso *ou* valor por conta própria, porque "peso/valor" é ambíguo. |
| Defeito ou erro da NovaTech | "(carga errada, avaria em trânsito): devolução sem custo para o cliente." | POL-001 §3.5 | DV | Pode encaminhar a avaria para "sinistro" (FAQ Item 38) e ignorar a regra normativa, ou o contrário. |
| Desistência do cliente | "(carga correta, sem defeito): o custo do frete reverso é do cliente, calculado com os mesmos multiplicadores do frete original." | POL-001 §3.5 | DV→FR | Pode aplicar os multiplicadores *atuais* (v2) em vez dos do frete original. Pode aplicar multiplicadores a carga abaixo de 500kg, que não tem multiplicador documentado. |
| Prazo expirado | "(solicitação após 7 dias úteis): não elegível para devolução padrão. Encaminhar ao Comercial para negociação caso a caso." | POL-001 §3.5 | DV | Pode responder "não pode devolver" e omitir o encaminhamento ao Comercial. |
| Mercadoria em trânsito | "Não se aplica a mercadorias ainda em trânsito (para essas, consultar PROC-088: Procedimento de Interceptação de Carga)." | POL-001 §2 | DV | Pode aplicar a POL-001 a uma carga em trânsito. Pode inventar o conteúdo da PROC-088, que não está na base. |

### 2.4 Devolução × carga danificada

> Par confundido com frequência e com conflito entre documentos.

| Termo | Definição (exatamente como no documento) | Origem | BC | Como um LLM poderia interpretar errado |
|---|---|---|---|---|
| Avaria em trânsito | Exemplo de "Defeito ou erro da NovaTech" → "devolução sem custo para o cliente" | POL-001 §3.5 | DV | Pode tratar como sinônimo exato de "carga danificada" (FAQ Item 38), que segue outro processo. |
| Carga danificada | "Carga danificada em trânsito tem processo diferente de devolução. O cliente precisa registrar a ocorrência em até 48h após o recebimento, com fotos e laudo se possível." | FAQ Item 38 — ⚠️ SRN (lacuna 1 das Notas do Anexo A) | AT | Pode apresentar como regra oficial. Pode trocar as 48h (horas, informal) pelos 7 dias úteis da devolução. |
| Sinistro / Jurídico | "isso passa pelo Jurídico, não pelo atendimento normal — encaminhe para o e-mail sinistros@novatech.com.br" | FAQ Item 38 — ⚠️ SRN | AT | Pode passar o e-mail como canal oficial sem aviso. |
| Reembolso integral por dano | "se comprovada responsabilidade nossa, reembolsa integralmente" | FAQ Item 38 — ⚠️ SRN | AT | Pode prometer reembolso integral antes da investigação. |

### 2.5 Frete especial: termos que mudam entre PROC-042 v1 e v2

| Termo | Definição (exatamente como no documento) | Origem | BC | Como um LLM poderia interpretar errado |
|---|---|---|---|---|
| Frete especial | "cálculo de frete especial aplicável a cargas com peso acima de 500kg" (texto igual nas duas versões, v2 com "parâmetros atualizados") | PROC-042 §1; PROC-042-v2 §1 | FR | Pode achar que é frete urgente, expresso, frágil ou de carga perigosa. Pode aplicar a 500kg exatos (o §1 diz "acima de"; o §2 começa a faixa em "500kg"). |
| Fórmula do frete especial | "**Valor do frete = Valor base × Multiplicador regional × Fator de peso**" (igual em v1 e v2) | PROC-042 §2; v2 §2 | FR | Pode somar os fatores ou incluir outros (seguro, desconto) na fórmula. |
| Valor base | "tarifa publicada na tabela mensal de fretes (disponível em `\\novatech-fs\comercial\tabelas\frete-base-AAAAMM.xlsx`)" | PROC-042 §2; v2 §2 | FR | Pode **inventar um valor em R$**. O conteúdo da tabela não está na base. |
| Multiplicador regional (v1) | Sul 1.2 · Sudeste 1.0 · Centro-Oeste 1.3 · Nordeste 1.4 · Norte 1.6 | PROC-042 §2.1 | FR | Pode misturar valores das duas tabelas ou usar só uma delas sem avisar. |
| Multiplicador regional (v2) | "(atualizados em novembro/2023)" Sul 1.3 · Sudeste 1.1 · Centro-Oeste 1.4 · Nordeste 1.5 · Norte 1.8 | PROC-042-v2 §2.1 | FR | Pode apresentar como a única versão válida. Nada formaliza a substituição (v2, cabeçalho). |
| Fator de peso (v1) | "1.0 para cargas de 500kg a 1.000kg; 1.2 para cargas de 1.001kg a 3.000kg; 1.5 para cargas acima de 3.000kg." | PROC-042 §2 | FR | Pode interpolar valores. Pode não ter faixa para pesos fracionados entre 1.000 e 1.001kg. |
| Fator de peso (v2) | "1.0 para cargas de 500kg a 1.000kg; 1.15 para cargas de 1.001kg a 3.000kg; 1.4 para cargas acima de 3.000kg." | PROC-042-v2 §2 | FR | Pode aplicar o fator da v1 junto com o multiplicador da v2. |
| Prazo de entrega do frete especial (v1) | "prazo padrão da rota **+ 2 dias úteis** adicionais para manuseio de carga pesada" | PROC-042 §3 | FR | Pode apresentar "2 dias" como o prazo total. |
| Prazo de entrega do frete especial (v2) | "prazo padrão da rota **+ 3 dias úteis** adicionais para manuseio e roteirização de carga pesada (anteriormente era + 2 dias na versão anterior)" | PROC-042-v2 §3 | FR | Pode ler "anteriormente" como prova de que a v1 está obsoleta. É um indício, não uma revogação formal. |
| Aprovação para carga acima de 5.000kg | "Cargas acima de 5.000kg requerem aprovação prévia do gerente de operações regional." (igual em v1 e v2) | PROC-042 §4; v2 §4 | FR | Pode confundir com o gerente de conta (SLA-2024). |
| Carga perigosa acima de 500kg | "seguem tabela específica (PROC-043: Frete de Cargas Perigosas)"; a v2 acrescenta: "a PROC-043 está em processo de revisão pelo Compliance e pode sofrer alterações" | PROC-042 §4; v2 §4 | FR | Pode aplicar a fórmula da PROC-042 a carga perigosa ou inventar o conteúdo da PROC-043. |
| Desconto de volume (v1) | "Descontos de volume (mais de 10 fretes especiais/mês para o mesmo cliente) devem ser negociados pelo Comercial e registrados em aditivo contratual." | PROC-042 §4 | FR | Pode tratar como desconto automático. |
| Desconto de volume (v2) | "a partir de 8 fretes especiais/mês para o mesmo cliente, aplicar desconto de 5% sobre o multiplicador regional. Acima de 15 fretes/mês, desconto de 10%. Descontos maiores requerem aprovação da Diretoria Comercial." | PROC-042-v2 §4 | FR | Pode aplicar os 5% sobre o valor final do frete em vez de sobre o multiplicador. Esta contradição com a v1 **não** está listada nas Notas do Anexo A. |
| Desconto automático | "Para clientes com mais de 10 fretes especiais por mês, existe desconto automático na tabela (veja PROC-042)." | FAQ Item 45 — ⚠️ SRN (contradiz v1 e v2) | AT | Pode repetir essa regra como se fosse normativa. |
| Disposições transitórias | "chamados abertos antes de 01/12/2023 que ainda estejam em processamento devem usar os multiplicadores da versão anterior (PROC-042 v1). Chamados novos a partir de 01/12/2023 devem usar os multiplicadores desta versão." | PROC-042-v2 §5 | FR/GD | Pode aplicar a transição também ao fator de peso e ao prazo (o texto fala só de *multiplicadores*). Pode resolver a contradição sozinho com base nesta seção. |
| Tabela antiga no contrato | "se o cliente reclamar do valor, pode ser que o contrato dele ainda esteja na tabela antiga" | FAQ Item 8 — ⚠️ SRN | AT | Pode criar a regra "contratos antigos usam v1". |

### 2.6 Termos que só aparecem no FAQ

> [!WARNING]
> Sem respaldo em documento normativo.

| Termo | Definição (exatamente como no documento) | Origem | BC | Como um LLM poderia interpretar errado |
|---|---|---|---|---|
| Seguro de carga | "A NovaTech oferece seguro de carga como adicional. O valor é 0,3% do valor declarado da mercadoria para cargas padrão e 0,8% para cargas perigosas. Detalhe: isso vale para contratos a partir de 2023." | FAQ Item 22 — ⚠️ SRN (lacuna 2 das Notas) | AT | Pode citar os percentuais como tabela oficial. "Cargas padrão" é mais um uso de "padrão". |
| Frete expresso | "Sim, mas precisa de autorização do Compliance e a documentação ANTT tem que estar atualizada. Na prática, demora uns 2 dias para conseguir a autorização" | FAQ Item 32 — ⚠️ SRN (contradição 4 das Notas) | AT | Pode tratar como modalidade oficial e o "2 dias" como SLA. O termo não é definido em lugar nenhum. |
| Prazo de trânsito por região | "Rotas para o Norte podem levar até 10 dias úteis. Para Sul/Sudeste, mais de 3 dias parado é estranho." | FAQ Item 27 — ⚠️ SRN | AT | Pode usar como "prazo padrão da rota", que não está definido. |
| Chamado de rastreamento | "Abra um chamado de rastreamento" | FAQ Item 27 — ⚠️ SRN | AT | Pode tratar como categoria oficial do Portal do Cliente. |
| Autonomia do atendente para desconto | "Atendente não tem autonomia para dar desconto." | FAQ Item 45 — ⚠️ SRN | AT | É uma regra plausível, mas não normativa. O LLM pode apresentar como política. |

### 2.7 Metadados documentais

| Termo | Definição (exatamente como no documento) | Origem | BC | Como um LLM poderia interpretar errado |
|---|---|---|---|---|
| Documento normativo | "Documento normativo — uso obrigatório pelo time de atendimento" | POL-001, cabeçalho | GD | Pode dar o mesmo peso a normativo e informal. |
| Documento contratual | "Documento contratual — os SLAs listados aqui são compromissos formais com o cliente" | SLA-2024, cabeçalho | GD | — |
| Documento informal | "Documento informal — NÃO validado por Compliance ou Operações. Representa o conhecimento prático do time, mas pode conter informações desatualizadas ou imprecisas." | FAQ, cabeçalho | GD | Pode usar o FAQ como fonte principal porque ele é "mais direto" e bate com o tom das perguntas. |
| Coexistência de versões | v1: "não possui indicação formal de vigência ou obsolescência no sistema da NovaTech. Coexiste com a versão PROC-042-v2." / v2: "não possui indicação formal de que substitui o PROC-042 v1. Ambos coexistem no SharePoint sem hierarquia clara." | PROC-042 e PROC-042-v2, cabeçalhos | GD | Pode supor que a versão com número maior é automaticamente a vigente. |

---

## 3. Termos sem definição na base

Estes termos são **usados** nos documentos, mas não são **definidos**. Não inventar definição para nenhum deles.

| Termo | Onde aparece | O que falta |
|---|---|---|
| Prazo padrão da rota | PROC-042 §3; v2 §3 | Valor ou tabela por rota. O FAQ Item 27 dá números informais. |
| Valor base (conteúdo) | PROC-042 §2; v2 §2 | A tabela mensal `frete-base-AAAAMM.xlsx` não está na base. |
| Região de destino (mapeamento) | PROC-042 §2.1; v2 §2.1 | Qual UF ou cidade pertence a cada região (ex.: Manaus, Salvador). |
| Frete padrão (abaixo de 500kg) | Implícito; lacuna 3 das Notas | Não há documento. |
| Frete expresso | FAQ Item 32 | Não há definição de modalidade, prazo ou preço. |
| Frete original | POL-001 §3.5 | Se significa a versão da PROC-042 usada no envio e como descobrir isso. |
| Chamado geral | SLA-2024 §2 | Não há definição explícita. Só dá para deduzir por exclusão de "incidente crítico". |
| Feriados nacionais (lista) e aplicação fora da POL-001 | POL-001 §3.1 | Se a mesma regra de dias úteis vale para SLA-2024 §5 e PROC-042 §3. |
| Operações/mês | SLA-2024 §1 | O que conta como "operação". |
| Valor declarado | SLA-2024 §3; FAQ Itens 22 e 27 | Onde fica registrado (CT-e? nota fiscal?). |
| Status desconhecido | SLA-2024 §3 | O que caracteriza esse status no tracking. |
| Irregularidade de documentação ou rastreamento | SLA-2024 §3 | Não há critérios. |
| "Mesmo problema" | SLA-2024 §3 | Como agrupar chamados. |
| Violação de SLA (contagem) | SLA-2024 §4 | Se é contada por cliente, por contrato ou por tier. |
| Portal de tracking × Portal do Cliente | SLA-2024 §2; POL-001 §3.3 | Se são o mesmo sistema. |
| Data de recebimento confirmada | POL-001 §3.1 | Quem confirma e o que acontece se não houver confirmação. |
| Aprovação (da devolução) | POL-001 §3.3 item 4 | Quem aprova e em quanto tempo depois da triagem. |
| Tratamento individual | POL-001 §3.2 | Não há procedimento (lacuna 4 das Notas). |
| Faixa especificada na nota fiscal | POL-001 §3.2 | Não há formato. |
| Centro de distribuição | POL-001 §3.3 item 5 | Qual CD recebe a devolução. |
| Gerente de operações regional × gerente de operações | PROC-042 §4; SLA-2024 §4 | Se é o mesmo papel. |
| Chamado "ainda em processamento" | PROC-042-v2 §5 | Quando um chamado deixa de estar em processamento. |
| Desconto "sobre o multiplicador regional" | PROC-042-v2 §4 | Se é percentual (1.8 × 0,95) ou subtração de pontos. |
| Peso de exatamente 500kg | PROC-042 §1 × §2 | O §1 diz "acima de 500kg", mas a faixa do §2 começa em "500kg". |
| Peso "peso/valor" (reembolso parcial) | POL-001 §3.4 | Se o reembolso é proporcional ao peso ou ao valor. |
| Descartado × obsoleto marcado | Cenário ("63 descartados por obsolescência") × ADR-0003 ("marcados, não excluídos") | Se "descartado" significa que o documento saiu do índice ou só foi marcado. |

---

## 4. Contradições adicionais encontradas

Além das 4 contradições listadas nas Notas do Anexo A:

1. **Desconto de volume:** v1 §4 (mais de 10 por mês, negociado, com aditivo) × v2 §4 (a partir de 8 por mês, 5% ou 10% sobre o multiplicador) × FAQ Item 45 (mais de 10 por mês, "automático").
2. **Avaria em trânsito:** POL-001 §3.5 (devolução sem custo) × FAQ Item 38 (processo separado, 48h, Jurídico).
3. **Pausa do relógio em incidente crítico de Silver e Standard:** a tabela do SLA-2024 §2 usa horas sem "úteis", mas o §5 só diz que o relógio "não pausa" para Gold. O comportamento para os outros tiers é indefinido.
4. **Critério de prioridade:** FAQ Item 27 (Gold ou valor acima de R$ 50.000 → prioridade alta) × SLA-2024 §3 (acima de R$ 100.000 e mais de 6 horas → incidente crítico).

---

## 5. Cobertura da linguagem ubíqua nos chunks (Anexo B)

O glossário cita o Anexo A completo, mas o LLM só vê os **chunks** recuperados. Nem todo termo do glossário chega até ele:

| Contexto | Chunks existentes | Termos ou regras do glossário **sem chunk** |
|---|---|---|
| Devoluções | POL-001-A, B, C, D | Cadeia de frio rompida e lacre violado (o POL-001-B só traz carga perigosa); reembolso em 5 dias úteis (§3.3 item 5); devolução parcial (§3.4); escopo e PROC-088 (§2) |
| Frete e Prazos | PROC-042-A, B, C; PROC-042v2-A a E | Todo o §4 da v1 (desconto de volume da v1, aprovação acima de 5.000kg, PROC-043); na v2, os itens de 5.000kg e PROC-043 do §4 |
| SLAs e Contratos | SLA-2024-A a E | Encaminhamento de SLA diferenciado ao Comercial (§1 Nota); disponibilidade do portal, gerente de conta e relatório (§2); medição e pausa do relógio (§5) |
| Atendimento (FAQ) | FAQ-03, 08, 15, 32, 38 | FAQ Itens 22 (seguro), 27 (prioridade alta), 41 (resposta × resolução), 45 (desconto automático e autonomia) |

**Simplificações nos chunks que mudam o sentido de termos:**
- **SLA-2024-D** tira "valor declarado" ("carga com valor acima de R$ 100.000") e reduz "irregularidade de documentação ou rastreamento" a "irregularidade".
- **Armadilha 4 do Anexo B** diz que cargas perigosas "NÃO podem ser devolvidas". A POL-001 §3.2 diz "não elegíveis para devolução **pelo processo padrão**" e prevê "tratamento individual". Para a linguagem ubíqua, vale o texto da POL-001.
- **Mapa de cobertura, "Frete para 600kg para Manaus?"**: exige só chunks da v2 e trata a v1 como "risco de contradição". Isso segue a ADR-0003 (priorizar a mais recente) e não a spec anterior (mostrar ambas). Ver pergunta 18.

---

## 6. Perguntas em aberto para validar com a NovaTech

1. **PROC-042 v1 × v2:** qual versão vale para chamados novos? A regra do v2 §5 ("a partir de 01/12/2023, usar esta versão") pode ser tratada como vigente, ou ela também está entre as 12 contradições pendentes com o Compliance?
2. A transição do v2 §5 vale só para os **multiplicadores**, ou também para o **fator de peso**, o **prazo adicional** e o **desconto de volume**?
3. Existe regra de contrato (FAQ Item 8, "contrato na tabela antiga") que prevalece sobre a data do chamado?
4. Quais são os **12 documentos** com contradição pendente? Os 5 do Anexo A estão entre eles?
5. Como mapear cidade ou UF de destino para **região** (Sul, Sudeste etc.)? O assistente pode usar conhecimento geográfico geral ou precisa de uma tabela oficial?
6. Carga de **exatamente 500kg** é frete especial?
7. Existe documento de **frete padrão** (abaixo de 500kg) e de **prazo padrão da rota**? Eles vão entrar na base?
8. **Avaria em trânsito** segue a POL-001 §3.5 (devolução sem custo) ou o processo de sinistro do FAQ Item 38?
9. Para **incidentes críticos de Silver e Standard**, o relógio pausa fora do horário comercial?
10. "Dias úteis" (sábados, domingos e feriados nacionais excluídos) vale também para SLA-2024 e PROC-042? E os feriados estaduais ou municipais?
11. POL-001 §3.2 lista as classes 1 a 6 da ANTT. Uma carga classificada pela ANTT fora dessa faixa segue o processo padrão de devolução?
12. O assistente deve **exibir** conteúdo que só existe no FAQ (seguro, frete expresso, carga danificada), mesmo com o rótulo "sem respaldo normativo", ou deve só apontar a lacuna?
13. "Chamado geral" é tudo que não é incidente crítico?
14. O desconto do v2 §4 "sobre o multiplicador regional" é multiplicativo (× 0,95) ou uma subtração?
15. Reembolso de devolução parcial: proporcional ao **peso** ou ao **valor** (POL-001 §3.4)?
16. A spec de RAG anterior cita "SLAs, frete e devoluções", mas não **prazos de entrega**, que é uma das 4 categorias do discovery. Prazos de entrega está no escopo?
17. Os **63 documentos "descartados por obsolescência"** saíram do índice ou continuam nele marcados como obsoletos (ADR-0003)?
18. O gabarito do Anexo B espera só a v2 para frete especial. Isso significa que a decisão entre "priorizar a mais recente" (ADR-0003) e "mostrar ambas" (spec anterior) já foi tomada a favor da ADR-0003?
19. Quais pares de categorias formam os **15% de perguntas cruzadas**? Existe amostra do discovery com essa distribuição?
20. Os chunks do Anexo B vão ser **re-gerados** para cobrir as regras que hoje não têm chunk (cadeia de frio, lacre, pausa do relógio de SLA, §4 da v1)?


____________________________________________________________________________________________________________________________________________________
2. Usando o **Claude**, escreva o `requirements.md` do query endpoint seguindo a estrutura SDD. As prior decisions devem referenciar as ADRs da fase anterior (simuladas no contexto). Os scope boundaries devem derivar dos bounded contexts definidos acima.
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

_______________________________________________________________________________________________________________________________________________________
3. Usando o **Claude Design**, crie um mockup da interface de resposta no Teams, coerente com os requirements.
Em anexo
_________________________________________________________________________________________________________________________________________________________
4. Itere: peça ao Claude que atue como Tech Lead e aponte ambiguidades. Ajuste.
Prompt enviado ao Claude
Claude quero que atue como Tech Lead e aponte ambiguidades. Ajuste.

Output obtido
Vou revisar como Tech Lead, comparando o mockup com o requirements v1.1 e o recorte de domínio. Primeiro levanto as ambiguidades, depois ajusto o canvas.

Editou um arquivo, executou um comando · 1 nota NovaTech Assistant — Resposta no Teams Claude Design·Apenas você 

Também atualizo o HTML para o GitHub com a versão revisada.

Executou um comando, arquivos compartilhados

Revisei o mockup como Tech Lead e o problema principal era de fonte: em alguns cards, a citação não continha a afirmação. Isso falha na regra (a) dos VCs. Corrigi isso no canvas e no HTML para o GitHub, e acrescentei uma sexta tela com estados que a spec deixa em aberto.

Citações que não sustentavam a afirmação (corrigido)

VC-04: a frase "não há documento de frete até 500kg" citava a PROC-042 §1, mas o trecho não diz isso. Também tirei o aviso de valor base, porque ele dava a entender que a fórmula da PROC-042 vale para 300kg. A ausência agora aparece como "Resultado da busca", sem fonte. VC-23: a parte do frete de volta citava a POL-001 §3.2 para algo que o trecho não diz. Virou uma lacuna declarada. VC-10 e VC-03: o banner de divergência, o aviso de conteúdo informal e a nota sobre a transição da v2 não tinham fonte. Agora têm.

Ambiguidades da spec que ficaram marcadas no mockup (borda tracejada)

Como citar uma ausência: a C-02 exige fonte em toda resposta, mas o aviso de lacuna (O-06) e o pedido de esclarecimento (VC-16) não têm documento para citar. Manaus → Norte: a tabela mostrava "Norte" como fato. Agora a linha diz "se Manaus = Norte", ligada à OQ-05. A transição da v2 não resolve a contradição: a regra do §5 está na própria v2, que não diz que substitui a v1. Também não está definido se o assistente deve pedir a data de abertura do chamado ao atendente (OQ-01). C-12 × C-03: a C-12 manda mostrar o normativo primeiro, e a C-03 manda não declarar versão vencedora. Quando o FAQ contradiz a POL-001, a ordem já é uma forma de hierarquia. Conteúdo sem chunk: o encaminhamento ao Comercial (VC-02) aparece no mockup, mas pela C-01 o modelo não poderia mostrá-lo enquanto não houver chunk (OQ-19). PROC-043: a C-13 permite citar o documento pelo nome, mas não há chunk que o traga. Não está claro se ele deve aparecer no VC-23. Casos sem comportamento definido: a espera de até 30 s (C-16 / OQ-15), o contexto fora da janela de 3 turnos (VC-16) e a pergunta fora de escopo (VC-17) agora estão na tela 6.

Viabilidade no Teams (a validar com o time do bot)

Adaptive Cards não permitem chips coloridos no meio do texto. Troquei as citações por texto monoespaçado entre colchetes, que dá para fazer. Não existe ação de copiar para a área de transferência, então removi o botão "Copiar resposta". A tabela v1 × v2 depende do elemento Table (schema 1.5) e de card em largura total. Continua faltando um selo para documento obsoleto (ADR-0003 / OQ-18). Deixei registrado, mas não desenhei.

Também tirei o ícone de telefone do encaminhamento ao Comercial, porque a base não traz contato para ele.

**Entregável:** O mapa de bounded contexts com linguagem ubíqua, o requirements.md, o mockup, e o histórico de iteração.

**Critérios de avaliação:**
- Os bounded contexts são coerentes com o domínio de logística (não são divisões técnicas como "frontend/backend").
- A linguagem ubíqua contém termos que um LLM confundiria sem definição explícita (ex: "Gold" é um tier de cliente, não o metal).
- Os outcomes no requirements.md são orientados a resultado do usuário, não a features técnicas.
- Os scope boundaries derivam dos bounded contexts (ex: "este módulo cobre o contexto 'Atendimento ao Cliente' — não cobre 'Gestão Documental' diretamente").
- Os verification criteria são testáveis pelo QA.
