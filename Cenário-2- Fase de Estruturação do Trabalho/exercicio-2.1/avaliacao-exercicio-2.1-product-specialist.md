# Avaliação do Exercício 2.1 — Recorte de domínio e spec SDD do query endpoint

| Campo | Valor |
|---|---|
| **Papel** | Product Specialist |
| **Cenário** | 2 — Estruturação do Trabalho |
| **Participante** | Jaqueline Santos |
| **Referências usadas** | `avaliacao-product-specialist.md` (cenário 2) e `prompt-avaliacao.md` (cenário 2) |
| **Não disponíveis na avaliação** | `avaliacao-foundation.md` (cenário 2) e Anexos A, B e C. As citações não foram conferidas contra os documentos originais. |
| **Data** | 30/09/2026 |

---

## Resumo

O recorte de domínio e o `requirements.md` são de nível sênior. Os 5 bounded contexts são de negócio e cada um tem fronteiras, relações e uma tabela dos termos que mudam de significado. O glossário usa citações exatas e tem uma coluna "como um LLM poderia interpretar errado". A spec segue a estrutura SDD, mostra o que cada ADR impõe ao módulo, registra 7 tensões e traz 23 critérios de verificação rastreados até os chunks.

O ponto fraco é a iteração com o "Tech Lead":

- o changelog diz que os apontamentos TL-01 a TL-20 **não foram aplicados**;
- as ambiguidades encontradas na revisão do mockup não entraram no `requirements.md`;
- o mockup anexado é a versão **anterior** à revisão.

---

## Scores por dimensão

| Dimensão | Score | Justificativa |
|---|---|---|
| D1 — Domínio Conceitual | 3 | O recorte é por domínio de negócio, com contexto core, de suporte e externos, *shared kernel* ("carga perigosa") e mapa de relações. Há um insight maduro: categoria de pergunta não é o mesmo que bounded context (§1.9). A estrutura SDD está completa: Outcomes, Scope, Constraints, Prior Decisions, VCs e Open Questions. |
| D2 — Uso de Ferramentas | 2 | Claude e Claude Design foram usados, e há evidência nos entregáveis. A iteração com o TL, porém, está incompleta. O prompt é mínimo ("atue como Tech Lead e aponte ambiguidades. Ajuste."). A lista de ambiguidades aparece só no resumo do Claude. Os apontamentos TL-01 a TL-20 da spec foram deixados para a v2, e o mockup revisado não foi anexado. |
| D3 — Qualidade do Entregável | 2 | O recorte e a spec estão completos e prontos para agentes (IDs, paths do repositório, `golden-queries.json`). Mas o mockup entregue ainda tem os erros que a própria revisão apontou e não mostra nenhum sinal de confiança. |
| D4 — Pensamento Crítico | 3 | Há julgamento próprio consistente: tensões T-01 a T-07 (ADR-0003 × spec; gabarito do Anexo B × spec; limite de 5 chunks × VC-03), análise de cobertura dos chunks (3 VCs sem chunk e 5 parciais), correção da armadilha 4 do Anexo B contra o texto da POL-001, a tensão "descartado × obsoleto marcado" e 25 termos sem definição. |
| D5 — Aplicabilidade ao Projeto | 3 | Referencia as ADR-0001 a 0004 com o impacto concreto de cada uma, o limite de contexto da ADR-0002, a spec de RAG do cenário 1, os paths do Anexo C e a arquitetura em 3 componentes. |

**Score do exercício: 2,6**

---

## Checklist da skill (Exercício 2.1)

| Critério | Nota | Evidência |
|---|---|---|
| Bounded contexts coerentes | 3 | Atendimento, Devoluções, Frete e Prazos, SLAs e Contratos, Gestão Documental. Nenhum contexto é técnico. |
| Linguagem ubíqua útil | 3 | Exemplos: Gold, "OU" lido como "E", "24h úteis" ≠ "1 dia", prioridade alta ≠ incidente crítico, "agendada" ≠ "realizada". |
| Linguagem extraída do Anexo A | 3 | As definições são citações literais com a seção. Os termos que só existem no FAQ estão marcados com ⚠️ SRN. |
| Outcomes orientados a resultado | 3 | São do ponto de vista do atendente: "o atendente consegue… em < 30 s", "sabe para onde encaminhar". |
| Scope derivado dos bounded contexts | 3 | A tabela 2.1 indica, para cada contexto, se o módulo cobre, consome ou não cobre, com a seção de origem. |
| Verification criteria testáveis | 3 | São 23 VCs com critério binário (contém / não contém), rastreados até os chunks. Quatro ainda dependem de [A DEFINIR] (VC-16 a VC-19). |
| Mockup (Claude Design) | 2 | Mostra fonte por afirmação, selo normativo/informal e feedback (Útil / Não útil). Não mostra confiança, e o arquivo entregue é anterior à revisão. |
| Prior decisions referenciam o cenário 1 | 3 | ADRs, limite de 5 chunks e 3 turnos, tratamento de contradições e vigência. |
| Iteração com "Tech Lead" | 2 | A revisão foi feita, mas o feedback não chegou à versão final da spec. |

---

## Verificação de artefatos machine-readable

**O que está bom:**

- IDs estáveis (O-, C-, T-, VC-, OQ-).
- Marcação padronizada `[A DEFINIR — validar com TL/NovaTech]`.
- Convenção de citação fixa (`DOC §seção`).
- Paths reais (`src/functions/query/`, `specs/*/`, `prompts/eval/golden-queries.json`).
- Mapa em Mermaid acompanhado de versão em texto.
- VCs com critérios automatizáveis ("contém 1.6 e 1.8", "não contém valor em R$").

**O que melhorar:**

- Algumas constraints misturam a regra com a justificativa (C-06, C-12). Separar "DEVE / NÃO DEVE" do motivo deixa a regra mais clara para um agente.
- O VC-05 exige só "pelo menos uma das" duas versões da PROC-042, o que contradiz a C-03 (mostrar as duas). O critério precisa exigir ambas.

---

## Problemas no mockup entregue

O HTML anexado ainda tem os problemas que a revisão do TL diz ter corrigido:

1. **VC-04:** a frase "A base não tem documento de frete para cargas até 500kg" cita a PROC-042 §1, mas esse trecho não diz isso. O aviso de *valor base* também dá a entender que a fórmula da PROC-042 vale para 300 kg.
2. **VC-23:** a frase "Sem cálculo de frete reverso…" cita a POL-001 §3.2, e esse trecho não fala de frete.
3. **VC-02:** o encaminhamento ao Comercial aparece na tela, mas não existe chunk que o sustente, o que viola a C-01.
4. **"Copiar resposta":** o botão continua no mockup, embora não seja viável em Adaptive Cards.
5. **Sexta tela** (estados de espera, contexto fora da janela, pergunta fora de escopo): não está no arquivo.
6. **Confiança:** não aparece. A rubrica espera "fonte + confiança + feedback". Se a decisão de produto for não mostrar uma nota numérica (OQ-11), deixe isso escrito e use os selos *normativo / informal / contradição / lacuna* como sinal de confiabilidade da fonte.

---

## Pontos fortes

1. **Glossário pensado para o LLM.** A coluna "como um LLM poderia interpretar errado" faz do glossário uma defesa contra alucinação. Exemplo: Standard × "processo padrão" × "cargas padrão".
2. **Tensões registradas em vez de resolvidas em silêncio.** T-01 (ADR-0003 × spec) e T-06 (7 chunks contra o limite de 5) são exatamente o que um Tech Lead precisa ver antes de implementar.
3. **Rastreabilidade de cada VC até os chunks.** Mostra que 3 VCs não podem passar com o Anexo B atual e transforma isso numa decisão a tomar (OQ-19).

---

## O que fazer antes de entregar (maior impacto com menos esforço primeiro)

1. **Anexar o mockup revisado.** O ideal é incluir as duas versões, com uma lista curta do que mudou. É rápido e resolve D3 e parte de D2.
2. **Criar a versão v1.2 da spec** com as ambiguidades apontadas pelo TL, registradas no changelog e nas seções certas:
   - exceção à C-02 para "não encontrei" e para pedido de esclarecimento, que não têm fonte para citar;
   - tensão C-12 × C-03 (mostrar o normativo primeiro já é uma forma de hierarquia);
   - regra de que conteúdo sem chunk não aparece, com impacto nos VC-02, VC-11, VC-13 e VC-14;
   - se o PROC-043 aparece ou não no VC-23;
   - limitações de Adaptive Cards (Table no schema 1.5, sem cópia para a área de transferência) como constraint da interface com `teams-bot`;
   - selo de documento obsoleto (ADR-0003 / OQ-18).
3. **TL-01 a TL-20:** aplicar agora, ou anexar a lista com a decisão sobre cada item (aplicado, adiado ou rejeitado, com o motivo). Hoje o avaliador não consegue ver esses apontamentos.
4. **Melhorar o prompt do TL.** Informe o que ele deve revisar (spec v1.1, recorte, mockup) e o que verificar (ambiguidade, testabilidade, viabilidade no Teams, coerência com as ADRs). Registre o output completo.
5. **Ajustes menores:**
   - corrigir o critério do VC-05;
   - conferir se as "4 categorias do discovery" são *prazos, frete, devolução e SLAs* ou *prazos, frete, devolução e outros*, como no cenário 1;
   - alinhar o resultado esperado do VC-11 com a cobertura de chunks.

Com os itens 1 a 3 feitos, a nota provavelmente fica entre 2,8 e 3,0.

---

## Classificação

**Aprovado com distinção (2,6)**

## Tópicos da trilha para reforço

Não é obrigatório, porque o score está acima de 2,5. Como opcional: em SDD, fazer o feedback de uma revisão virar uma nova versão da spec, com changelog.

---

> **Observação:** esta avaliação feita por IA não substitui a validação do avaliador humano. Isso vale principalmente para as citações dos Anexos A, B e C e para o mockup revisado, que não foi entregue para esta avaliação.
