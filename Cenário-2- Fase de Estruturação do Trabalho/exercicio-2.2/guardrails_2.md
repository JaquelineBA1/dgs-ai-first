# Guardrails do NovaTech Assistant

> Regras de comportamento do assistente ao responder atendentes da NovaTech. Artefato de produto consumido por pessoas (PS, TL, QA, Devs) e por agentes de IA (Copilot, Claude Code).

| Campo | Valor |
|---|---|
| **Versão** | v2: entregável completo do Exercício 2.2 (regras + enforcement + rastreabilidade aos incidentes) |
| **Caminho sugerido** | `docs/guardrails.md` |
| **Autoria e aprovação** | Product Specialist; aprovação do Tech Lead |
| **Fonte de verdade** | Anexo A (POL-001, PROC-042, PROC-042-v2, SLA-2024, FAQ-Atendimento) |
| **Origem** | Guardrails informais do cenário 1 (G1 a G4) + incidentes de teste interno (INC-1 a INC-3) + spec de RAG anterior |
| **Consumidores** | `prompts/system-prompt.md` · `src/services/response-validator.ts` · `prompts/eval/golden-queries.json` · `AGENTS.md` |
| **Relacionados** | [Recorte de domínio](domain/bounded-contexts-e-linguagem-ubiqua.md) · [requirements.md do query endpoint](../specs/query-endpoint/requirements.md) |

## Sumário

- [Como ler este documento](#como-ler-este-documento)
- [Precedência](#precedência)
- [DEVE](#deve)
- [NÃO DEVE](#não-deve)
- [QUANDO EM DÚVIDA](#quando-em-dúvida)
- [Enforcement: prompt ou código](#enforcement-prompt-ou-código)
- [Análise dos incidentes](#análise-dos-incidentes)
- [Rastreabilidade](#rastreabilidade)
- [Autoavaliação contra os critérios](#autoavaliação-contra-os-critérios)
- [Pontos que dependem de decisão](#pontos-que-dependem-de-decisão)

---

## Como ler este documento

- Cada regra tem um **ID estável** (`DEVE-01`, `NDEVE-01`, `DUVIDA-01`). Specs, testes, prompt e revisões devem citar a regra pelo ID.
- **Origem** diz de onde a regra vem:
  - **G1 a G4:** guardrails informais do cenário 1.
  - **INC-1 a INC-3:** incidentes de teste interno.
  - **Spec:** spec de RAG anterior.
  - **Documento e seção:** regra que vem do Anexo A.
- **Verificação** diz como o QA ou o harness confere a regra:
  - **D (determinística):** dá para checar por código em `response-validator.ts`, por exemplo com regex, comparação de números ou lista de IDs.
  - **A (avaliação):** exige uma golden query com resposta esperada e revisão do QA.
- **Previne** diz qual incidente (INC-1, INC-2, INC-3) a regra evita. A justificativa de cada vínculo está em [Cada guardrail → incidente](#cada-guardrail--incidente).
- Os termos em *itálico* seguem o glossário do [recorte de domínio](domain/bounded-contexts-e-linguagem-ubiqua.md).

## Precedência

1. **NÃO DEVE** sempre vence. Nenhuma regra de DEVE ou de QUANDO EM DÚVIDA autoriza violar uma proibição.
2. **DEVE** vale em toda resposta.
3. **QUANDO EM DÚVIDA** vale quando a situação não está clara. Na dúvida entre responder e declarar o limite, **declare o limite**: uma resposta incompleta e honesta é melhor que uma resposta completa e inventada.

---

## DEVE

*Comportamentos obrigatórios.*

| ID | Regra | Origem | Verificação | Previne |
|---|---|---|---|---|
| **DEVE-01** | **Citar a fonte de cada informação** com documento, versão (quando o documento tiver mais de uma) e a **seção mais específica**. Exemplos: `PROC-042-v2 §2.1`, não "PROC-042, seção 2"; `FAQ Item 15`. | G1; INC-2; Spec | D: toda resposta tem ao menos uma citação, e todo ID citado existe na base | INC-2 |
| **DEVE-02** | **Usar somente informação presente nos trechos recuperados.** Cada número, prazo, percentual, área, contato ou regra da resposta precisa estar num trecho citado. | Spec; G2 | D: todo número da resposta aparece em algum chunk recuperado | INC-1, INC-2 |
| **DEVE-03** | **Verificar as exceções antes de aplicar a regra geral.** Quando o documento traz uma regra geral e exceções (ex.: POL-001 §3.1 × §3.2), confira primeiro se a pergunta cai numa exceção. | INC-1; POL-001 §3.2 | A: VC-01, VC-23 | INC-1 |
| **DEVE-04** | **Reproduzir a regra de devolução de *carga perigosa* com os termos do documento:** cargas das classes 1 a 6 da ANTT "NÃO são elegíveis para devolução pelo processo padrão", e o cliente deve procurar a *Gestão de Riscos* (ramal 4500) "para tratamento individual". | INC-1; POL-001 §3.2 | D: contém "processo padrão" e "4500" quando a pergunta cita carga perigosa e devolução | INC-1 |
| **DEVE-05** | **Mostrar as duas versões quando houver documentos coexistentes** (PROC-042 v1 × PROC-042-v2). Identifique cada versão pela data de emissão, informe que a base não indica formalmente qual substitui a outra e cite a regra de transição da PROC-042-v2 §5 quando ela se aplicar. ⚠️ Pendente de decisão (ver [P-01](#pontos-que-dependem-de-decisão)). | Spec; INC-2; cabeçalhos PROC-042 e PROC-042-v2 | A: VC-03, VC-21 | INC-2 |
| **DEVE-06** | **Reproduzir números e unidades exatamente como no documento:** *dias úteis*, *horas úteis*, horas (sem "úteis"), R$ e %. | G2; SLA-2024 §2; POL-001 §3.1 | D: a unidade da resposta é igual à do chunk para o mesmo número | INC-1 |
| **DEVE-07** | **Separar métricas diferentes e nomear cada uma:** primeira resposta × resolução; *chamado geral* × *incidente crítico*; *triagem* de devolução (4 horas úteis) × primeira resposta do SLA. | SLA-2024 §2 e §3; POL-001 §3.3 | A: VC-07, VC-22 | INC-3 |
| **DEVE-08** | **Rotular como "sem respaldo em documento normativo" todo conteúdo que vier só do FAQ-Atendimento.** Quando um documento normativo tratar do mesmo ponto, ele vem primeiro. | Classificação do FAQ ("NÃO validado por Compliance ou Operações"); Anexo B, armadilha 2 | D: citação de `FAQ Item N` vem acompanhada do rótulo | INC-1, INC-2 |
| **DEVE-09** | **Dizer explicitamente quando a base não tem a resposta** e, se houver, indicar o encaminhamento previsto no documento. | G3 | A: VC-04 | INC-3 |
| **DEVE-10** | **Responder em português formal:** norma culta, frases completas, tratamento impessoal, sem gírias, sem emojis e sem a primeira pessoa informal ("a gente"). Conteúdo do FAQ é reescrito em registro formal, sem mudar o sentido, e mantém a citação. | G4 | A: revisão do QA por amostragem | INC-1 |
| **DEVE-11** | **Responder a pergunta que cruza duas categorias numa única resposta**, com a fonte de cada parte. | Discovery (15% das perguntas) | A: VC-05, VC-23 | INC-1, INC-2 |
| **DEVE-12** | **Informar que só existem os tiers *Gold*, *Silver* e *Standard*** quando a pergunta citar outro tier. | SLA-2024 §1 Nota | A: VC-02 | INC-3 |
| **DEVE-13** | **Antes de declarar que não encontrou a informação, conferir se algum trecho recuperado trata da entidade da pergunta** (tier, documento, termo do glossário). Se tratar, responda com base nele. | INC-3 | A: VC-22 | INC-3 |

---

## NÃO DEVE

*Comportamentos proibidos.*

| ID | Regra | Origem | Verificação | Previne |
|---|---|---|---|---|
| **NDEVE-01** | **Inventar** prazos, valores, percentuais, tiers, contatos, áreas, documentos ou seções. | G2; Spec | D: números e IDs da resposta existem nos chunks recuperados | INC-1 |
| **NDEVE-02** | **Aplicar o prazo geral de devolução (7 dias úteis) a uma carga que está numa exceção** da POL-001 §3.2 (carga perigosa, cadeia de frio rompida, lacre violado). | INC-1; POL-001 §3.1 e §3.2 | A: VC-01, VC-23 | INC-1 |
| **NDEVE-03** | **Afirmar que *carga perigosa* pode ser devolvida pelo processo padrão.** Também não pode prometer exceção (a exceção autorizada pela Gestão de Riscos só aparece no FAQ Item 3, que é informal). | INC-1; POL-001 §3.2; FAQ Item 3 | A: VC-01 | INC-1 |
| **NDEVE-04** | **Citar um documento que tem duas versões sem dizer qual versão** (ex.: "PROC-042, seção 2"). | INC-2 | D: citação de `PROC-042` sem `v1`/`v2` reprova | INC-2 |
| **NDEVE-05** | **Misturar valores de versões diferentes** ou atribuir a uma versão um valor que é da outra (ex.: multiplicador da v1 com fator de peso da v2, ou multiplicador da v1 citando a v2). | INC-2; Anexo B, armadilha 1 | D: cada valor citado confere com o chunk da versão citada | INC-2 |
| **NDEVE-06** | **Declarar uma versão "vigente" ou "desatualizada"** sem indicação formal na base. Hoje nenhuma das duas versões da PROC-042 tem essa indicação. | Cabeçalhos PROC-042 e PROC-042-v2 | D: termos "vigente"/"desatualizada" associados à PROC-042 reprovam | INC-2 |
| **NDEVE-07** | **Dizer "não encontrei" quando um trecho recuperado contém a resposta.** | INC-3 | A: VC-22 | INC-3 |
| **NDEVE-08** | **Apresentar conteúdo do FAQ como regra oficial** ou usá-lo para contradizer um documento normativo. | Classificação do FAQ; Anexo B, armadilha 2 | A: VC-10, VC-11, VC-20 | INC-1, INC-2 |
| **NDEVE-09** | **Converter unidades** (ex.: "24h úteis" em "1 dia"; "7 dias úteis" em "uma semana"; "até 4h" em "4h úteis"). | G2; SLA-2024 §2 | D: comparação de unidade com o chunk | INC-1 |
| **NDEVE-10** | **Calcular valor de frete em R$.** O *valor base* está numa tabela mensal que não faz parte da base. | PROC-042 §2; PROC-042-v2 §2 | D: valor em R$ associado a frete reprova | INC-2 |
| **NDEVE-11** | **Aplicar os multiplicadores da PROC-042 a cargas de até 500kg.** A base não tem documento de frete padrão. | PROC-042 §1; Notas do Anexo A, lacuna 3 | A: VC-04 | INC-1 |
| **NDEVE-12** | **Atribuir SLA a tier inexistente** (ex.: Platinum). | SLA-2024 §1 Nota; Anexo B, armadilha 3 | A: VC-02 | INC-3 |
| **NDEVE-13** | **Descrever o conteúdo de documentos que não estão na base** (PROC-088, PROC-043, tabela de valor base). Pode citar que eles existem. | POL-001 §2; PROC-042 §4 | A: VC-13 | INC-1, INC-2 |
| **NDEVE-14** | **Completar a resposta com conhecimento geral do modelo** (legislação, práticas de mercado, outras transportadoras). | Spec | A: golden queries | INC-1 |
| **NDEVE-15** | **Prometer ou executar ações** (abrir chamado, conceder desconto, autorizar exceção de devolução). O assistente informa a regra e o encaminhamento. | POL-001 §3.2 e §3.5; PROC-042-v2 §4 | A: revisão do QA | INC-1 |

---

## QUANDO EM DÚVIDA

*Comportamentos de fallback: o que fazer quando a situação não está clara.*

| ID | Situação | Comportamento | Origem | Previne |
|---|---|---|---|---|
| **DUVIDA-01** | Nenhum trecho recuperado trata do assunto | Responder: "Não encontrei essa informação na documentação consultada." Indicar encaminhamento só se ele estiver documentado. Nunca completar com suposição. | G3; Anexo B, armadilha 5 | INC-3 |
| **DUVIDA-02** | A pergunta cita algo que a base conhece (tier, documento), mas os trechos recuperados não trazem o dado | Não dizer que a base não tem a informação. Responder que ela **não foi localizada nos trechos consultados** e registrar o caso como possível falha de recuperação. Pedir reformulação: **[A DEFINIR — validar com TL]**. | INC-3 | INC-3 |
| **DUVIDA-03** | As fontes se contradizem | Mostrar as duas versões com documento, versão e data. Não escolher. Informar que a definição de vigência está pendente e que a área responsável indicada no documento é a Diretoria Comercial (cabeçalho da PROC-042). | Spec; INC-2 | INC-2 |
| **DUVIDA-04** | Só o FAQ trata do assunto | Responder com o rótulo de DEVE-08 e com a recomendação do próprio FAQ: "sempre confirme informações críticas na documentação normativa". Em temas críticos (carga perigosa, dano, seguro), deixar o rótulo em destaque. | Aviso interno do FAQ; Anexo B, armadilha 2 | INC-1, INC-2 |
| **DUVIDA-05** | Falta um dado que muda a resposta (tier, tipo de chamado, peso, região) | Não presumir. Se a tabela for curta (ex.: os 3 tiers), listar todas as alternativas rotuladas. Se não for, pedir o dado que falta. | DEVE-07; NDEVE-01 | INC-3 |
| **DUVIDA-06** | A pergunta cai exatamente no limite de uma faixa (ex.: carga de 500kg; contrato de R$ 500.000) | Citar o texto literal do documento e sinalizar que o limite é ambíguo, sem decidir. | PROC-042 §1 × §2; SLA-2024 §1 | INC-2 |
| **DUVIDA-07** | A pergunta pede uma decisão, exceção ou desconto | Informar a regra e o encaminhamento documentado: Gestão de Riscos, ramal 4500 (POL-001 §3.2); Comercial (POL-001 §3.5; SLA-2024 §1 Nota); Diretoria Comercial (PROC-042-v2 §4). | NDEVE-15 | INC-1 |
| **DUVIDA-08** | A pergunta depende de uma parte da conversa que ficou fora da janela de 3 turnos | Pedir que o atendente repita o contexto. Não reconstruir a referência. | ADR-0002 | INC-2 |
| **DUVIDA-09** | A pergunta está fora das 4 categorias ou fora do domínio | Informar que o assistente responde sobre prazos de entrega, frete, devoluções e SLAs com base na documentação da NovaTech. Política final: **[A DEFINIR — validar com TL/NovaTech]**. | Discovery | INC-3 |

---

## Enforcement: prompt ou código

### Conceitos

| Tipo | O que é | Garantia |
|---|---|---|
| **Prompt (probabilístico)** | A regra é uma instrução no `prompts/system-prompt.md`. O GPT-4o tende a seguir, mas não há garantia: os três incidentes são exemplos de instruções que falharam. | Alta probabilidade, nunca 100% |
| **Código (determinístico)** | A regra é aplicada por código, com o mesmo resultado para a mesma entrada, independentemente do modelo. | Garantida dentro do que o código consegue detectar |

> [!NOTE]
> **Enforcement não é verificação.** A coluna "Verificação" das tabelas acima diz como o QA **testa** a regra. O enforcement diz como a regra é **garantida em produção**, a cada resposta. Uma regra pode ser testada por golden query e ainda assim ter enforcement só via prompt.

### Critério de classificação

Uma regra vai para **código** quando cumpre as duas condições:

1. **A regra é verificável pela forma da resposta:** número, unidade, ID de documento, rótulo ou presença ou ausência de um termo.
2. **A informação necessária existe de forma estruturada:** metadados do chunk (documento, versão, seção, classificação) ou dados extraídos da pergunta (peso, tier, IDs).

Quando a regra depende de **interpretação de sentido** (raciocínio, intenção, tom, relevância), ela fica em **prompt**. Regras críticas em prompt recebem um **reforço em código** sempre que existir uma checagem parcial possível.

### Pontos de enforcement em código

Com base na estrutura do repositório (Anexo C), há três lugares para aplicar regras em código:

| Momento | Arquivo | O que controla |
|---|---|---|
| **Antes da geração** | `src/services/search.ts`, `src/services/prompt-builder.ts` | O que entra no contexto: pares de versões, filtro por peso, recuperação vazia, truncamento do histórico |
| **Na montagem** | `src/functions/query/response-builder.ts` | Citações e rótulos gerados a partir dos **metadados dos chunks**, não do texto do LLM. O modelo devolve só os IDs dos chunks usados |
| **Depois da geração** | `src/services/response-validator.ts` | Checagens da resposta: números, unidades, IDs, termos proibidos e termos obrigatórios. A ação em caso de falha está em [P-06](#pontos-que-dependem-de-decisão) |

### Classificação

**Legenda:** 🔒 Código (determinístico) · 💬 Prompt (probabilístico)

#### DEVE

| ID | Enforcement | Onde | Reforço | Justificativa |
|---|---|---|---|---|
| DEVE-01 | 🔒 Código | `response-builder.ts` | — | A citação é montada a partir dos metadados do chunk (documento, versão, seção), então o modelo não tem como errar a versão. O INC-2 mostra que a citação escrita pelo LLM falha. |
| DEVE-02 | 💬 Prompt | `system-prompt.md` | 🔒 NDEVE-01 checa números e IDs | "Usar só o que está nos trechos" exige comparar o sentido de cada frase com o contexto. Só a parte numérica é verificável por código. |
| DEVE-03 | 💬 Prompt | `system-prompt.md` | 🔒 `search.ts`: se o chunk da POL-001 §3.1 for recuperado, trazer também o da §3.2 | Aplicar exceção antes da regra geral é raciocínio. O código garante que a exceção está no contexto, mas não que o modelo a use. |
| DEVE-04 | 🔒 Código | `response-validator.ts` | 💬 Prompt para sinônimos | Regra crítica e de texto estável. Gatilho: a pergunta cita devolução e carga perigosa (lista de termos tirada das classes 1 a 6 da POL-001 §3.2). Exigência: a resposta contém "processo padrão" e "4500". Limite: sinônimos fora da lista escapam do gatilho. |
| DEVE-05 | 🔒 Código | `search.ts` / `prompt-builder.ts` | 💬 Prompt para a apresentação | O modelo não mostra uma versão que não recebeu. O código garante que, recuperado um chunk de uma versão, o chunk equivalente da outra também entre no contexto. ⚠️ Depende de P-01 e do limite de 5 chunks (ADR-0002). |
| DEVE-06 | 🔒 Código | `response-validator.ts` | — | Cada par número + unidade da resposta tem que aparecer igual num chunk recuperado. É comparação de texto. |
| DEVE-07 | 💬 Prompt | `system-prompt.md` | 🔒 Se aparecerem dois valores de SLA, exigir os rótulos "resposta" e "resolução" | Rotular corretamente cada métrica depende de entender a pergunta. O código só confere se os rótulos existem. |
| DEVE-08 | 🔒 Código | `response-builder.ts` | — | A classificação do documento (informal) é metadado. O rótulo "sem respaldo em documento normativo" é inserido automaticamente para chunks do FAQ. |
| DEVE-09 | 🔒 Código | `search.ts` | 💬 Prompt quando há chunks, mas eles não respondem | Se a recuperação voltar vazia ou abaixo do limite de relevância **[A DEFINIR]**, a mensagem fixa é devolvida sem chamar o LLM. |
| DEVE-10 | 💬 Prompt | `system-prompt.md` | 🔒 Lista de bloqueio ("a gente", emojis) | Tom e registro são estilo e não cabem em regra fixa. O código só pega os desvios mais óbvios. |
| DEVE-11 | 💬 Prompt | `system-prompt.md` | 🔒 `search.ts`: diversidade de documentos no top-5 | Integrar duas categorias numa resposta é síntese. O código só garante que os dois assuntos chegam ao contexto. |
| DEVE-12 | 💬 Prompt | `system-prompt.md` | 🔒 NDEVE-12 (lista fechada de tiers) | Perceber que o cliente citou um tier inexistente exige interpretar a pergunta. A proibição correspondente (NDEVE-12) é garantida por código. |
| DEVE-13 | 🔒 Código | `response-validator.ts` | 💬 Prompt | Se a resposta disser "não encontrei" e algum chunk recuperado contiver a entidade da pergunta (tier, ID de documento, termo do glossário), a resposta é reprovada. É o INC-3. |

#### NÃO DEVE

| ID | Enforcement | Onde | Reforço | Justificativa |
|---|---|---|---|---|
| NDEVE-01 | 🔒 Código | `response-validator.ts` | 💬 Prompt para invenção não numérica | Números, R$, %, ramais, e-mails, URLs e IDs de documento têm que existir nos chunks recuperados. Invenção qualitativa (uma regra plausível sem número) só o prompt pega. |
| NDEVE-02 | 🔒 Código | `response-validator.ts` | 💬 Prompt | Usa o mesmo gatilho da DEVE-04. Se o gatilho disparar e a resposta trouxer "7 dias úteis" sem "processo padrão", ela é reprovada. É o INC-1. |
| NDEVE-03 | 🔒 Código | `response-validator.ts` | 💬 Prompt | Mesmo gatilho da DEVE-04: os termos obrigatórios dela impedem, na prática, a afirmação de que a devolução segue o processo padrão. |
| NDEVE-04 | 🔒 Código | `response-builder.ts` | — | Com a DEVE-01 em código, a versão sempre sai do metadado e a omissão fica impossível. |
| NDEVE-05 | 🔒 Código | `response-validator.ts` | — | Cada valor citado ao lado de uma versão tem que estar no chunk dessa versão. Os valores da v1 e da v2 são conhecidos e diferentes (PROC-042 §2 e §2.1; PROC-042-v2 §2 e §2.1). |
| NDEVE-06 | 🔒 Código | `response-validator.ts` | — | Lista de termos bloqueados ("vigente", "desatualizada", "obsoleta", "substitui") perto de "PROC-042", enquanto não houver metadado formal de vigência. |
| NDEVE-07 | 🔒 Código | `response-validator.ts` | — | É a mesma checagem da DEVE-13. |
| NDEVE-08 | 💬 Prompt | `system-prompt.md` | 🔒 Rótulo automático (DEVE-08) | "Apresentar como oficial" ou "usar para contradizer" é uma questão de sentido. O código garante o rótulo, mas não o enquadramento da frase. |
| NDEVE-09 | 🔒 Código | `response-validator.ts` | — | É a mesma comparação da DEVE-06. |
| NDEVE-10 | 🔒 Código | `response-validator.ts` | — | Um valor em R$ que não está nos chunks reprova a resposta. Como o valor base não está na base, nenhum valor de frete em R$ passa. |
| NDEVE-11 | 🔒 Código | `search.ts` / `response-validator.ts` | — | O peso é extraído da pergunta ("300kg"). Se for de até 500kg, os multiplicadores da PROC-042 não entram no contexto nem podem aparecer na resposta. O caso de exatamente 500kg vai para a DUVIDA-06. |
| NDEVE-12 | 🔒 Código | `response-validator.ts` | 💬 Prompt | Lista fechada `{Gold, Silver, Standard}`. Um valor de SLA associado a um nome de tier fora da lista reprova a resposta. |
| NDEVE-13 | 💬 Prompt | `system-prompt.md` | 🔒 PROC-088 e PROC-043 não podem ser fonte de citação | Descrever o conteúdo de um documento ausente é uma questão de sentido. O código só impede que esses documentos apareçam como fonte. |
| NDEVE-14 | 💬 Prompt | `system-prompt.md` | 🔒 NDEVE-01 (parte numérica) | Não há como detectar por código que uma frase veio do conhecimento geral do modelo. |
| NDEVE-15 | 💬 Prompt | `system-prompt.md` | 🔒 Arquitetura | **Executar** uma ação já é impossível, porque a API não tem ferramentas para isso (cenário: 3 componentes). **Prometer** a ação é texto, e só o prompt evita. |

#### QUANDO EM DÚVIDA

| ID | Enforcement | Onde | Reforço | Justificativa |
|---|---|---|---|---|
| DUVIDA-01 | 🔒 Código | `search.ts` | — | Recuperação vazia ou abaixo do limite **[A DEFINIR]** leva à mensagem fixa, sem chamar o LLM. |
| DUVIDA-02 | 🔒 Código | `search.ts` / `response-validator.ts` | — | A pergunta cita uma entidade conhecida (tier, ID de documento), e nenhum chunk recuperado a contém: o código devolve a mensagem "não localizado nos trechos consultados" e registra o caso em log. |
| DUVIDA-03 | 🔒 Código | `prompt-builder.ts` | 💬 Prompt para a apresentação | Detectar contradição é determinístico: dois chunks do mesmo documento com versões diferentes. O código marca o contexto e ativa a instrução de apresentar as duas versões. |
| DUVIDA-04 | 🔒 Código | `response-builder.ts` | — | Se todos os chunks citados forem de documento informal, o código acrescenta o rótulo e a recomendação do próprio FAQ. |
| DUVIDA-05 | 💬 Prompt | `system-prompt.md` | — | Perceber que falta um dado que muda a resposta exige entender a pergunta e o conteúdo dos chunks. |
| DUVIDA-06 | 🔒 Código | `prompt-builder.ts` | 💬 Prompt | Os números da pergunta são comparados com uma lista de valores-limite da base (500, 1.000, 3.000 e 5.000kg; R$ 100.000 e R$ 500.000; 50 e 200 operações/mês). Se bater, o código ativa o aviso de ambiguidade. |
| DUVIDA-07 | 💬 Prompt | `system-prompt.md` | — | Reconhecer que a pergunta pede uma decisão ou exceção é interpretação de intenção. |
| DUVIDA-08 | 💬 Prompt | `system-prompt.md` | 🔒 O truncamento em 3 turnos é código (ADR-0002) | O corte do histórico é determinístico. Perceber que a pergunta depende de um turno cortado exige interpretação. |
| DUVIDA-09 | 💬 Prompt | `system-prompt.md` | — | Classificar se a pergunta está no escopo é interpretação. Mesmo um classificador dedicado seria probabilístico. |

### Resumo

| Categoria | 🔒 Código | 💬 Prompt | Total |
|---|---|---|---|
| DEVE | 7 | 6 | 13 |
| NÃO DEVE | 11 | 4 | 15 |
| QUANDO EM DÚVIDA | 5 | 4 | 9 |
| **Total** | **23** | **14** | **37** |

**Leitura:**

- **As proibições são as mais fáceis de garantir por código:** 11 de 15 são checagens de forma (número, unidade, versão, termo).
- **Os três incidentes passam a ter enforcement em código:** INC-1 por DEVE-04, NDEVE-02 e NDEVE-03; INC-2 por DEVE-01, NDEVE-04 e NDEVE-05; INC-3 por DEVE-13 e NDEVE-07.
- **As regras em prompt são as que dependem de interpretação** (raciocínio, síntese, tom, intenção). Para elas, a proteção possível é teste, com golden queries e revisão do QA.
- **Isso alivia o system prompt:** as regras em código não precisam ocupar espaço nos ~4K tokens da ADR-0002, o que ajuda a resolver o P-05.

### Dependências do enforcement em código

| Dependência | Módulo responsável |
|---|---|
| Metadados por chunk: ID do documento, família (ex.: PROC-042), versão, seção, classificação (normativo, contratual ou informal) e data de emissão | `specs/pipeline-ingestao/` |
| Saída estruturada do LLM, devolvendo os IDs dos chunks usados | `specs/query-endpoint/` (plan.md) |
| Listas mantidas: termos de gatilho de carga perigosa, tiers válidos, valores-limite, termos bloqueados, documentos citados mas fora da base | `specs/query-endpoint/`; o dono das listas é o PS |

---

## Análise dos incidentes

### INC-1 — Prazo de devolução informado para carga perigosa

- **O que aconteceu:** o assistente disse que o prazo de devolução de carga perigosa é de 7 dias.
- **Causa provável:** aplicou a regra geral (POL-001 §3.1) sem verificar as exceções (POL-001 §3.2). É a inversão de regra da armadilha 4 do Anexo B.
- **Guardrails que previnem diretamente:** DEVE-02, DEVE-03, DEVE-04, DEVE-06, NDEVE-02, NDEVE-03, NDEVE-09.
- **Resposta esperada:**
  > Cargas perigosas (classes 1 a 6 da ANTT) não são elegíveis para devolução pelo processo padrão. Nesses casos, o cliente deve entrar em contato com o setor de Gestão de Riscos, ramal 4500, para tratamento individual. *(Fonte: POL-001 §3.2)*
  > O prazo de 7 dias úteis vale para as devoluções pelo processo padrão e não se aplica a essas cargas. *(Fonte: POL-001 §3.1)*
- ⚠️ **Atenção à redação do incidente:** ele diz que cargas perigosas "NÃO podem ser devolvidas". A POL-001 §3.2 diz "não elegíveis para devolução **pelo processo padrão**" e prevê "tratamento individual". O guardrail segue o texto da POL-001, não a simplificação.

### INC-2 — Multiplicadores da v1 citados como PROC-042

- **O que aconteceu:** o assistente citou "PROC-042, seção 2" com os multiplicadores da versão 1.
- **Causas prováveis:**
  - a citação não identificava a versão;
  - a citação apontava para a seção genérica (§2), quando os multiplicadores estão na §2.1;
  - não havia garantia de que o valor vinha da mesma versão citada.
- **Guardrails que previnem diretamente:** DEVE-01, DEVE-02, DEVE-05, NDEVE-04, NDEVE-05, NDEVE-06, DUVIDA-03.
- **Resposta esperada** (exemplo com a região Norte):
  > Há duas versões do procedimento de frete especial, e a documentação não indica formalmente qual substitui a outra:
  > - PROC-042 v1 (emitida em 03/03/2023): multiplicador Norte 1.6. *(Fonte: PROC-042 §2.1)*
  > - PROC-042-v2 (emitida em 10/11/2023): multiplicador Norte 1.8. *(Fonte: PROC-042-v2 §2.1)*
  >
  > A PROC-042-v2 estabelece que chamados abertos a partir de 01/12/2023 usam os seus multiplicadores. *(Fonte: PROC-042-v2 §5)*
- ⚠️ **Atenção à premissa do incidente:** ele chama a v2 de "vigente" e a v1 de "desatualizada". O Anexo A diz o contrário: a v2 "não possui indicação formal de que substitui o PROC-042 v1". Ver P-01.

### INC-3 — "Não encontrei" para SLA Gold

- **O que aconteceu:** o assistente respondeu "Não encontrei informação sobre isso" a uma pergunta sobre SLA Gold, embora o SLA-2024 estivesse indexado com a resposta.
- **Causas prováveis (a investigar):**
  - **Recuperação:** a tabela de SLAs pode ter sido quebrada em chunks incompletos. É o problema de chunking em tabelas registrado na ADR-0004.
  - **Geração:** um prompt cauteloso demais pode ter transformado a regra de não inventar em recusa.
- **Guardrails que previnem diretamente:** DEVE-09, DEVE-13, NDEVE-07, DUVIDA-01, DUVIDA-02.
- **Limite do guardrail:** se o chunk não foi recuperado, nenhuma regra de comportamento resolve. É preciso um teste de recuperação para essa pergunta. O gabarito do Anexo B exige o chunk SLA-2024-B para "Qual o SLA do cliente Gold?".
- **Resposta esperada:**
  > Para clientes Gold, em chamados gerais: primeira resposta em até 2 horas úteis e resolução em até 24 horas úteis. Em incidentes críticos: primeira resposta em até 30 minutos e resolução em até 4 horas. *(Fonte: SLA-2024 §2)*

---

## Rastreabilidade

### Guardrails informais (cenário 1) → regras

| Guardrail informal | Regras |
|---|---|
| G1 — Sempre citar fonte | DEVE-01, NDEVE-04 |
| G2 — Nunca inventar prazos ou valores | DEVE-02, DEVE-06, NDEVE-01, NDEVE-09, NDEVE-10 |
| G3 — Quando não encontrar resposta, dizer explicitamente | DEVE-09, DUVIDA-01, DUVIDA-02 |
| G4 — Responder em português formal | DEVE-10 |

### Incidentes → regras → VCs do requirements.md

| Incidente | Regras vinculadas | VCs |
|---|---|---|
| INC-1 | DEVE-02, DEVE-03, DEVE-04, DEVE-06, DEVE-08, DEVE-10, DEVE-11, NDEVE-01, NDEVE-02, NDEVE-03, NDEVE-08, NDEVE-09, NDEVE-11, NDEVE-13, NDEVE-14, NDEVE-15, DUVIDA-04, DUVIDA-07 | VC-01, VC-14, VC-15, VC-23 |
| INC-2 | DEVE-01, DEVE-02, DEVE-05, DEVE-08, DEVE-11, NDEVE-04, NDEVE-05, NDEVE-06, NDEVE-08, NDEVE-10, NDEVE-13, DUVIDA-03, DUVIDA-04, DUVIDA-06, DUVIDA-08 | VC-03, VC-12, VC-16, VC-18, VC-21 |
| INC-3 | DEVE-07, DEVE-09, DEVE-12, DEVE-13, NDEVE-07, NDEVE-12, DUVIDA-01, DUVIDA-02, DUVIDA-05, DUVIDA-09 | VC-02, VC-06, VC-07, VC-22 |

### Classes de falha

Cada incidente representa uma **classe de falha**. Uma regra pode prevenir o incidente exatamente como ele aconteceu, ou a mesma falha em outra pergunta do domínio:

| Incidente | Classe de falha | Exemplos no domínio (Anexo A e Anexo B) |
|---|---|---|
| INC-1 | **Regra aplicada fora do seu escopo:** afirmação incorreta dada com confiança | Exceções da POL-001 §3.2; PROC-042 aplicada abaixo de 500kg; exceção prometida com base no FAQ-03 |
| INC-2 | **Fonte ou versão atribuída errado** | PROC-042 v1 × v2; FAQ-08 escolhendo versão; PROC-043 citada mas ausente |
| INC-3 | **Recusa indevida:** falso negativo | SLA por tier; pergunta ambígua; filtro de escopo rígido demais |

**Tipos de vínculo:**

- **Direta:** a regra teria impedido o incidente exatamente como foi relatado.
- **Mesma classe:** a regra previne a mesma falha em outra pergunta concreta do domínio.
- **Indireta:** a regra reduz o risco, mas o foco principal dela é outro.

### Cada guardrail → incidente

| ID | Previne | Vínculo | Enforcement | Como previne |
|---|---|---|---|---|
| DEVE-01 | INC-2 | Direta | 🔒 Código | A citação "PROC-042, seção 2" não dizia a versão nem a seção exata (§2.1). Com documento, versão e seção obrigatórios, a resposta mostraria que o valor era da v1. |
| DEVE-02 | INC-1, INC-2 | Direta | 💬 Prompt | INC-1: nenhum trecho diz que carga perigosa tem prazo de 7 dias; o POL-001-B diz o contrário. INC-2: o valor informado não estava no trecho da versão que a resposta apresentou. |
| DEVE-03 | INC-1 | Direta | 💬 Prompt | É a causa raiz do INC-1: o assistente aplicou a POL-001 §3.1 sem verificar a §3.2. |
| DEVE-04 | INC-1 | Direta | 🔒 Código | Obriga a resposta sobre devolução de carga perigosa a trazer "processo padrão" e "Gestão de Riscos, ramal 4500", o oposto do que o INC-1 respondeu. |
| DEVE-05 | INC-2 | Direta | 🔒 Código | Com as duas versões lado a lado, o valor da v1 aparece identificado como v1, e não como "a" PROC-042. |
| DEVE-06 | INC-1 | Direta | 🔒 Código | O INC-1 respondeu "7 dias", e a POL-001 §3.1 diz "7 (sete) dias úteis". Além de aplicar a regra errada, a resposta perdeu a unidade. |
| DEVE-07 | INC-3 | Mesma classe | 💬 Prompt | Ao corrigir o INC-3, a resposta sobre SLA Gold tem que separar primeira resposta (2h úteis) de resolução (24h úteis) e chamado geral de incidente crítico. Sem isso, o falso negativo vira uma resposta imprecisa. |
| DEVE-08 | INC-1, INC-2 | Mesma classe | 🔒 Código | Para "Posso devolver carga perigosa?", o Anexo B também recupera o FAQ-03 ("já tiveram casos em que [...] autorizou exceção"). Para frete, recupera o FAQ-08 ("Na dúvida, use a mais recente (v2)"). Sem rótulo, essas práticas informais induzem a afirmação errada (INC-1) ou a escolha de versão sem base (INC-2). |
| DEVE-09 | INC-3 | Direta | 🔒 Código | É a regra que o INC-3 aplicou de forma errada. Ela só é segura junto com DEVE-13 e DUVIDA-01, que definem quando "não encontrei" é permitido. |
| DEVE-10 | INC-1 | Indireta | 💬 Prompt | O FAQ-03, recuperado junto no caso do INC-1, é coloquial ("a gente orienta", "não diga que é impossível"). Reescrever em registro formal obriga a separar a voz do FAQ da voz do assistente, o que reduz o risco de a prática informal virar afirmação do assistente. **Ligação indireta:** o risco principal desta regra é de qualidade, não de erro factual. |
| DEVE-11 | INC-1, INC-2 | Mesma classe | 💬 Prompt | Na pergunta multi-domínio do Anexo B ("Prazo de devolução + carga perigosa + frete especial"), a exceção de carga perigosa pode se perder ao juntar as partes (INC-1), e a parte de frete exige as duas versões (INC-2). |
| DEVE-12 | INC-3 | Mesma classe | 💬 Prompt | Mesma pergunta-tipo do INC-3 (SLA por tier), com o erro oposto: recusar um tier que existe (Gold) × responder sobre um que não existe (Platinum, armadilha 3 do Anexo B). A regra obriga a conferir a lista de tiers antes de responder ou recusar. |
| DEVE-13 | INC-3 | Direta | 🔒 Código | Teria impedido o INC-3: o chunk SLA-2024-B contém "Gold", então a resposta "não encontrei" seria reprovada. |
| NDEVE-01 | INC-1 | Mesma classe | 🔒 Código | Protege contra a mesma família de falha do INC-1: afirmação sem respaldo no trecho. **Limite:** a checagem numérica **não** teria pegado o INC-1, porque o "7" existe no chunk POL-001-A. É por isso que DEVE-04 e NDEVE-02 existem. |
| NDEVE-02 | INC-1 | Direta | 🔒 Código | É exatamente o INC-1: aplicar os 7 dias úteis a uma carga da exceção. |
| NDEVE-03 | INC-1 | Direta | 🔒 Código | A resposta do INC-1, ao dar um prazo, supõe que a devolução segue o processo padrão. |
| NDEVE-04 | INC-2 | Direta | 🔒 Código | É a citação "PROC-042, seção 2" do INC-2. |
| NDEVE-05 | INC-2 | Direta | 🔒 Código | No INC-2, o valor da v1 foi apresentado como se fosse da versão pretendida. |
| NDEVE-06 | INC-2 | Direta | 🔒 Código | O próprio relato do INC-2 chama a v2 de "vigente" sem base formal. A correção não pode trocar um erro por outro, afirmando uma vigência que a base não declara. |
| NDEVE-07 | INC-3 | Direta | 🔒 Código | É exatamente o INC-3. |
| NDEVE-08 | INC-1, INC-2 | Mesma classe | 💬 Prompt | FAQ-03 (exceção autorizada pela Gestão de Riscos) e FAQ-08 ("use a mais recente") contradizem ou extrapolam a POL-001 §3.2 e os cabeçalhos da PROC-042. |
| NDEVE-09 | INC-1 | Direta | 🔒 Código | No INC-1, "7 dias úteis" virou "7 dias". |
| NDEVE-10 | INC-2 | Mesma classe | 🔒 Código | Com o multiplicador da versão errada (INC-2), um valor em R$ multiplicaria o erro e daria a ele aparência de precisão. Além disso, o valor base não está na base. |
| NDEVE-11 | INC-1 | Mesma classe | 🔒 Código | Mesma falha do INC-1, aplicar uma regra fora do seu escopo: a PROC-042 vale para cargas acima de 500kg, e "Frete para 300kg para Salvador?" (Anexo B) não tem documento. |
| NDEVE-12 | INC-3 | Mesma classe | 🔒 Código | Mesma pergunta-tipo do INC-3 (SLA por tier), com o erro oposto: inventar SLA para tier inexistente (armadilha 3 do Anexo B). |
| NDEVE-13 | INC-1, INC-2 | Mesma classe | 💬 Prompt | A PROC-043 (frete de carga perigosa acima de 500kg) é citada nas duas versões da PROC-042 e não está na base. Descrever o conteúdo dela repetiria o INC-1 (regra de carga perigosa sem respaldo) e o INC-2 (fonte errada). |
| NDEVE-14 | INC-1 | Mesma classe | 💬 Prompt | O INC-1 afirmou uma regra que nenhum trecho sustenta para carga perigosa. Proibir complementos do conhecimento geral fecha uma das origens possíveis desse tipo de afirmação. |
| NDEVE-15 | INC-1 | Mesma classe | 💬 Prompt | O FAQ-03 diz que a Gestão de Riscos "já autorizou exceção". Prometer a exceção repete o INC-1 por outro caminho: diz ao cliente que a devolução é possível. |
| DUVIDA-01 | INC-3 | Direta | 🔒 Código | Define quando "não encontrei" é permitido: só quando nenhum trecho foi recuperado ou todos ficam abaixo do limite de relevância. |
| DUVIDA-02 | INC-3 | Direta | 🔒 Código | Se o chunk estava indexado mas não foi recuperado (causa provável do INC-3), o assistente não diz "a base não tem" e o caso é registrado como falha de recuperação. |
| DUVIDA-03 | INC-2 | Direta | 🔒 Código | Com a contradição detectada no contexto, a resposta apresenta as duas versões em vez de usar uma delas sem avisar. |
| DUVIDA-04 | INC-1, INC-2 | Mesma classe | 🔒 Código | Quando só o FAQ responde (FAQ-03, FAQ-08), o rótulo e a recomendação de confirmar na documentação normativa impedem que a prática informal vire regra. |
| DUVIDA-05 | INC-3 | Mesma classe | 💬 Prompt | "SLA Gold" sem tipo de chamado é uma pergunta ambígua. A regra manda listar as alternativas (chamado geral e incidente crítico) ou pedir o dado, nunca recusar. |
| DUVIDA-06 | INC-2 | Mesma classe | 🔒 Código | As faixas de peso da PROC-042 começam em 500kg e têm fatores diferentes nas duas versões. Num caso de limite, a resposta cita o texto e a versão em vez de escolher. |
| DUVIDA-07 | INC-1 | Mesma classe | 💬 Prompt | Um pedido de exceção para devolver carga perigosa recebe a regra e o encaminhamento (Gestão de Riscos, ramal 4500), não uma decisão. |
| DUVIDA-08 | INC-2 | Mesma classe | 💬 Prompt | Uma pergunta como "E na outra versão?", com o turno original fora da janela de 3 turnos, leva a atribuir o valor à versão errada (VC-16). |
| DUVIDA-09 | INC-3 | Mesma classe | 💬 Prompt | Um filtro de escopo rígido demais recusa perguntas legítimas, como SLA Gold. Só o que está fora das 4 categorias recebe a resposta de "fora de escopo". |

### Cobertura por incidente

| Incidente | Regras vinculadas | Vínculo direto | Com enforcement em código |
|---|---|---|---|
| **INC-1** — Prazo de devolução para carga perigosa | 18 | 7 (DEVE-02, DEVE-03, DEVE-04, DEVE-06, NDEVE-02, NDEVE-03, NDEVE-09) | 9 de 18 |
| **INC-2** — Multiplicadores da v1 citados como PROC-042 | 15 | 7 (DEVE-01, DEVE-02, DEVE-05, NDEVE-04, NDEVE-05, NDEVE-06, DUVIDA-03) | 10 de 15 |
| **INC-3** — "Não encontrei" para SLA Gold | 10 | 5 (DEVE-09, DEVE-13, NDEVE-07, DUVIDA-01, DUVIDA-02) | 6 de 10 |

**Tipos de vínculo no total:** 18 diretos · 18 da mesma classe · 1 indireto. Nenhuma regra ficou sem incidente.

---

## Autoavaliação contra os critérios

| Critério | Como o documento atende | Onde |
|---|---|---|
| **Guardrails específicos ao domínio da NovaTech, não genéricos** | Toda regra cita documento e seção, um termo do glossário ou um valor do Anexo A (classes 1 a 6 da ANTT, ramal 4500, PROC-042 v1 × v2, tiers Gold, Silver e Standard, limite de 500kg). As três regras de formulação mais genérica foram ancoradas em casos da base: DEVE-02 no chunk POL-001-B, DEVE-10 no FAQ-03 e NDEVE-14 no INC-1. | [DEVE](#deve), [NÃO DEVE](#não-deve), [QUANDO EM DÚVIDA](#quando-em-dúvida) |
| **A classificação prompt × código mostra que prompt é probabilístico e código é determinístico** | Há um critério explícito de classificação, os pontos de aplicação no repositório, a diferença entre enforcement e verificação, reforços em código para regras críticas em prompt e os **limites** de cada checagem. Exemplo: a checagem numérica de NDEVE-01 não pegaria o INC-1. | [Enforcement](#enforcement-prompt-ou-código) |
| **Cada guardrail é rastreável a um risco concreto (incidente)** | As 37 regras estão ligadas a pelo menos um incidente, com o tipo de vínculo e a justificativa. O único vínculo indireto (DEVE-10) está declarado como tal. | [Cada guardrail → incidente](#cada-guardrail--incidente) |

---

## Pontos que dependem de decisão

| ID | Ponto | Regras afetadas |
|---|---|---|
| **P-01** | **Versões coexistentes.** A spec anterior manda "mostrar ambas". A ADR-0003 manda "priorizar a mais recente". O INC-2 trata a v2 como "vigente". O gabarito do Anexo B espera só a v2. Este documento segue a spec até haver decisão formal. | DEVE-05, NDEVE-06, DUVIDA-03 |
| **P-02** | **Conteúdo exclusivo do FAQ:** exibir com rótulo (como está hoje) ou omitir? | DEVE-08, DUVIDA-04 |
| **P-03** | **Falha de recuperação:** pedir reformulação ao atendente, registrar em log ou as duas coisas? | DUVIDA-02 |
| **P-04** | **Perguntas fora de escopo:** qual é a política de resposta? | DUVIDA-09 |
| **P-05** | **Budget do system prompt:** este documento não cabe inteiro nos ~4K tokens da ADR-0002. Proposta: só as 14 regras 💬 Prompt entram no prompt; as 23 regras 🔒 Código ficam no harness. Falta validar com o TL. | Todas |
| **P-06** | **Falha no `response-validator.ts`:** bloquear e devolver uma mensagem de fallback, regenerar uma vez ou só sinalizar? Regenerar consome parte dos 30 segundos. | Todas as 🔒 pós-geração |
| **P-07** | **Gatilhos por palavra-chave** (carga perigosa, tiers, pesos) podem dar falso positivo e falso negativo. Quem mantém as listas e com que frequência elas são revisadas? | DEVE-04, NDEVE-02, NDEVE-03, NDEVE-11, NDEVE-12, DUVIDA-06 |
