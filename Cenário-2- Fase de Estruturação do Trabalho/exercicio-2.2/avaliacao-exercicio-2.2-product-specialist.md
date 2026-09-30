# Avaliação do Exercício 2.2 — Guardrails formalizados

| Campo | Valor |
|---|---|
| **Papel** | Product Specialist |
| **Cenário** | 2 — Estruturação do Trabalho |
| **Participante** | Jaqueline Santos |
| **Referências usadas** | `avaliacao-product-specialist.md` (cenário 2) e `prompt-avaliacao.md` (cenário 2) |
| **Não disponíveis nesta avaliação** | `avaliacao-foundation.md` (cenário 2) e Anexos A, B e C. As citações não foram conferidas contra os documentos originais. |
| **Versão avaliada** | `docs/guardrails.md` v2 (entregável final) |
| **Data** | 30/09/2026 |

---

## Resumo

O documento é um artefato de produto maduro. Tem 37 regras com ID estável, divididas nas três categorias (DEVE, NÃO DEVE, QUANDO EM DÚVIDA). Cada regra informa a origem, como é verificada, como é garantida (prompt ou código) e qual incidente previne. O critério de classificação é explícito e separa **enforcement** de **verificação**. Os pontos de aplicação no repositório estão mapeados. O documento reconhece os limites das próprias checagens e contesta a redação de dois incidentes com base no Anexo A.

Os pontos fracos são três:

- **Contradições no desenho do validador.** Algumas checagens em código reprovariam as respostas que o próprio documento exige.
- **Classificações otimistas.** Duas regras aparecem como "código" quando a garantia é só parcial.
- **Sem registro do uso do Claude.** Não há prompts nem outputs.

---

## Scores por dimensão

| Dimensão | Score | Justificativa |
|---|---|---|
| D1 — Domínio Conceitual | 3 | Mostra que entende a diferença entre probabilístico e determinístico. O critério de classificação tem duas condições: a regra é verificável pela forma da resposta e a informação existe de forma estruturada. O documento separa enforcement de verificação e define três pontos de aplicação: antes da geração, na montagem e depois da geração. Regras críticas em prompt ganham reforço em código. Os limites aparecem explícitos (por exemplo, "a checagem numérica **não** teria pegado o INC-1"). |
| D2 — Uso de Ferramentas | 2 | Há progressão v1 → v1.1 → v2, mas cada versão corresponde a uma tarefa do enunciado, e não a uma iteração sobre feedback. Não há prompt, output nem histórico da conversa com o Claude, e a "Autoavaliação" foi escrita pela própria autora. |
| D3 — Qualidade do Entregável | 2 | O documento está completo, prescritivo e fácil de ler por máquina. Mas o desenho do validador tem contradições internas que impediriam a implementação sem ajuste (ver "Problemas encontrados"). |
| D4 — Pensamento Crítico | 3 | Há julgamento próprio. A autora contesta a redação do INC-1 ("NÃO podem ser devolvidas" × "não elegíveis pelo processo padrão") e a premissa do INC-2 ("vigente" sem base formal). Aponta que o INC-3 pode ser falha de recuperação, que nenhuma regra de comportamento resolve. Declara o único vínculo indireto (DEVE-10) e lista 7 pontos que dependem de decisão, incluindo o P-06 (o que fazer quando o validador reprova) e o P-07 (falso positivo em gatilhos por palavra-chave). |
| D5 — Aplicabilidade ao Projeto | 3 | Usa a ADR-0002 (orçamento de ~4K tokens para o prompt, 5 chunks, 3 turnos de histórico), a ADR-0003 (P-01), a ADR-0004 (chunking de tabelas no INC-3) e a spec do cenário 1. Conecta as regras aos VCs do `requirements.md` (2.1) e aos paths do Anexo C. |

**Score do exercício: 2,6**

---

## Checklist da skill (Exercício 2.2)

| Critério | Nota | Evidência |
|---|---|---|
| 3 categorias presentes | 3 | 13 DEVE, 15 NÃO DEVE e 9 QUANDO EM DÚVIDA, todas preenchidas, com regra de precedência entre elas. |
| Classificação prompt vs código | 3 | As 37 regras estão classificadas, com justificativa, arquivo de destino e reforço. Há duas classificações otimistas (ver problemas 3 e 4). |
| Rastreabilidade aos 3 incidentes | 3 | Todas as regras estão ligadas a pelo menos um incidente, com tipo de vínculo (direta, mesma classe ou indireta). Alguns vínculos marcados como "diretos" não se sustentam (ver problema 5). |
| Específicos ao domínio NovaTech | 3 | O documento cita carga perigosa (classes 1 a 6, ramal 4500), tiers, multiplicadores, versões da PROC-042, o limite de 500 kg, PROC-043 e PROC-088, e itens do FAQ. |

---

## Verificação de artefatos machine-readable

**Prescritivo e fácil de seguir por um agente:**

- Os IDs estáveis (`DEVE-01`, `NDEVE-04`, `DUVIDA-02`) podem ser citados em código, testes e revisões.
- Várias regras têm critério binário. Exemplos: "citação de `PROC-042` sem `v1`/`v2` reprova" (NDEVE-04) e a lista fechada `{Gold, Silver, Standard}` (NDEVE-12).
- Cada regra tem destino explícito no repositório (`response-builder.ts`, `response-validator.ts`, `search.ts`, `prompt-builder.ts`, `system-prompt.md`).
- A precedência é determinística: NÃO DEVE vence DEVE, que vence QUANDO EM DÚVIDA.

**Narrativo ou vago demais para um agente:**

- **Precedência, item 3** ("uma resposta incompleta e honesta é melhor…") é uma justificativa, não uma regra. Pode ficar como nota.
- **DEVE-10** ("norma culta, tratamento impessoal") não tem critério verificável. Sugestão: uma lista curta de proibições (primeira pessoa informal, gírias, emojis) e um exemplo de reescrita.
- **As listas mantidas** (gatilhos de carga perigosa, tiers válidos, valores-limite, termos bloqueados, documentos fora da base) só existem como descrição. Para que o validador e o Copilot usem essas listas, elas precisam virar arquivos, por exemplo `config/guardrails/*.json`, com o PS como dono.

---

## Problemas encontrados

1. **NDEVE-06 reprova o texto que DEVE-05 exige.** A lista de termos bloqueados da NDEVE-06 inclui "substitui" perto de "PROC-042". A DEVE-05 obriga a resposta a "informar que a base não indica formalmente qual substitui a outra", e a resposta esperada do INC-2 usa essa frase. Do jeito que está, o validador reprovaria toda resposta correta sobre a contradição. **Correção:** bloquear só afirmações ("a v2 substitui", "a v1 está obsoleta") ou liberar a frase padrão da DEVE-05.

2. **DEVE-06 reprova as respostas esperadas do próprio documento.** A regra manda reproduzir unidades "exatamente como no documento", e a checagem é por comparação de texto. As respostas esperadas do INC-3 usam "2 **horas** úteis", "30 **minutos**" e "4 **horas**", enquanto o SLA-2024 §2 usa "2h úteis", "30min" e "4h". A DEVE-10 (reescrever em registro formal) também puxa em outra direção. **Correção:** criar uma tabela de equivalências aceitas (h = horas; min = minutos) e **proibir só a troca de unidade** ("úteis" ↔ corridas, horas ↔ dias).

3. **NDEVE-03 não é garantida por código.** A justificativa diz que os termos obrigatórios da DEVE-04 impedem, na prática, a afirmação proibida. Não impedem. A resposta "carga perigosa pode ser devolvida pelo processo padrão; ligue para o ramal 4500" contém "processo padrão" e "4500" e passaria. O mesmo vale para a NDEVE-02, porque "7 dias úteis pelo processo padrão" também passaria. **Correção:** reclassificar as duas como 💬 Prompt + 🔒 checagem parcial e sustentá-las com golden queries (VC-01, VC-23).

4. **DEVE-01 só é garantida em parte.** O código garante a **forma** da citação (documento, versão e seção vindos dos metadados). Não garante que cada afirmação esteja ligada ao chunk certo: o modelo pode usar um valor da v1 e marcar o ID do chunk da v2. Quem fecha essa brecha é a NDEVE-05. **Correção:** marcar a DEVE-01 como "código para o formato + NDEVE-05 para a correspondência".

5. **Alguns vínculos "diretos" com o INC-1 não se sustentam.**
   - **DEVE-02:** o "7" existe no chunk POL-001-A, então "usar só o que está nos trechos" não teria impedido o INC-1. O próprio documento reconhece isso na NDEVE-01.
   - **DEVE-06 e NDEVE-09:** o relato do incidente ("7 dias") é uma paráfrase de quem registrou. Não prova que a resposta perdeu o "úteis".

   Esses três vínculos deveriam ser "mesma classe". Os vínculos "mesma classe" de DUVIDA-08 com o INC-2 e de DUVIDA-09 com o INC-3 também são fracos. Uma saída mais honesta é permitir a origem "risco do domínio (armadilha do Anexo B)" quando nenhum incidente se aplica de fato.

6. **Enforcement pós-geração depende do P-06.** Enquanto não estiver definido o que acontece quando o validador reprova (bloquear, regenerar ou só sinalizar), as regras 🔒 aplicadas depois da geração são **detecção**, não **garantia**. Isso precisa aparecer na tabela de classificação, e não só em "Pontos que dependem de decisão".

7. **O INC-3 não tem regra de harness para a recuperação.** O documento diz, corretamente, que se o chunk não foi recuperado nenhuma regra de comportamento resolve. Mesmo assim, não transforma isso em regra. **Sugestão:** uma regra determinística de teste em CI, por exemplo "a golden query 'Qual o SLA do cliente Gold?' deve recuperar o chunk SLA-2024-B". É o guardrail que de fato previne o INC-3.

---

## Pontos fortes

1. **Enforcement tratado como engenharia.** O documento define três pontos de aplicação, trata citações montadas a partir dos metadados e não do texto do LLM, e explica que as regras em código aliviam o orçamento de ~4K tokens do prompt (P-05).
2. **Leitura crítica dos incidentes.** Corrige o que os relatos simplificam (INC-1 e INC-2) e separa falha de recuperação de falha de geração (INC-3).
3. **Classes de falha.** Agrupar os incidentes em "regra fora do escopo", "fonte ou versão errada" e "recusa indevida" torna a rastreabilidade útil para prevenir erros novos, e não só para repetir os três casos relatados.

---

## O que fazer antes de entregar (maior impacto com menos esforço primeiro)

1. **Corrigir as duas contradições do validador:** a NDEVE-06 com a DEVE-05 e a DEVE-06 com as respostas esperadas (problemas 1 e 2). É rápido e é o que mais pesa em D3.
2. **Reclassificar a NDEVE-02, a NDEVE-03 e a DEVE-01** como garantia parcial, explicando o limite (problemas 3 e 4).
3. **Rever os vínculos "diretos" do INC-1** e marcar como "mesma classe" os que não se sustentam (problema 5).
4. **Acrescentar a regra de harness para a recuperação do INC-3** e deixar claro na tabela que as regras pós-geração dependem do P-06 (problemas 6 e 7).
5. **Registrar o uso do Claude:** os prompts de cada tarefa e o que você aceitou, ajustou ou rejeitou. Isso sobe D2.
6. **Entregar só a v2**, com um changelog curto (v1 → v1.1 → v2), em vez de repetir o documento três vezes.
7. **Opcional:** transformar as listas mantidas em arquivos (`config/guardrails/*.json`). Isso deixa o artefato pronto para o exercício 2.3 (AGENTS.md).

Com os itens 1 a 5 feitos, a nota provavelmente fica entre 2,8 e 3,0.

---

## Classificação

**Aprovado com distinção (2,6)**

## Tópicos da trilha para reforço

Não é obrigatório, porque o score está acima de 2,5. Como opcional, vale revisar o tópico **Harness**: a diferença entre detecção e garantia no enforcement determinístico, e testes de recuperação como guardrail em CI.

---

> **Observação:** esta avaliação foi feita por IA e não substitui a validação do avaliador humano, principalmente quanto às citações dos Anexos A, B e C.
