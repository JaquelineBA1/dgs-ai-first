Avaliação do Exercício 1.2 — Design de jornada com componente de IA (3ª rodada)

Papel: Product Specialist · Cenário: 1 — Entendimento e Contexto

Resumo

Esta versão tem um salto grande de qualidade. O novo prompt é bem construído: define contexto, tarefa, regras que proíbem inventar dados, pede para marcar [PROPOSTA] o que ainda precisa de validação e especifica o formato de saída. Com isso, o output cobre 5 fallbacks, um ciclo de feedback com triagem, validação e reindexação, e 8 guardrails com fonte. O diagrama está correto e completo.

O ponto fraco agora é a sua análise crítica: ela descreve o output antigo, não este. Além disso, o documento continua dizendo que existe uma V2 que não aparece.

Scores por dimensão
Dimensão	Score	Justificativa
D1 — Domínio Conceitual	3	A jornada agora cobre as limitações de um RAG: sem resposta na base (FA), contradição resolvida pela regra de transição da v2 §5 (FB), fonte só informal (FC), remissão a documento que não está indexado (FE) e premissa falsa. O ciclo de feedback separa três causas de erro (documento, busca, prompt), tem teste de regressão e reindexação, e trata a manutenção como contínua.
D2 — Uso de Ferramentas	3	O prompt é exemplar: a regra "use somente…", a marcação [PROPOSTA], as perguntas em aberto em vez de suposições e o formato pensado para virar diagrama. O Claude Design gerou um artefato de alto nível. Para ficar sólido, falta registrar o prompt usado no Claude Design.
D3 — Qualidade do Entregável	2	O diagrama está excelente. O texto tem problemas (detalhes abaixo): o output quebra a própria numeração do ciclo, a seção R6 fica incompleta e faltam partes do formato que o prompt pediu. Há também o mesmo ``` solto depois da análise crítica, que transforma o resto do documento em bloco de código.
D4 — Pensamento Crítico	2	Há julgamento seu na construção do prompt: você levou para ele o que aprendeu no 1.1 (a §5, o FAQ-38 contra a POL-001 §3.5, a proibição de inventar dados). Mas a análise depois do output não corresponde ao que o output diz e não aponta nenhum erro dele.
D5 — Aplicabilidade ao Projeto	3	Usa todos os números do cenário (320 chamados, 45 pessoas, 60%, meta de 12 para menos de 2 minutos), o Teams + SharePoint, os donos por área, os documentos e as seções.

Score do exercício: 2,6

Checklist da skill
Critério	Nota	Evidência
3 fluxos presentes	3	Os três estão completos, e o fallback tem 5 gatilhos.
Guardrails específicos ao domínio	3	São 8, cada um com regra, risco evitado, exemplo e fonte.
Feedback loop completo	3	R1 a R6 vão da sinalização até o retorno ao atendente, com triagem por causa, validação, reindexação e retirada das versões obsoletas.
Diagrama (Claude Design)	3	É coerente com o texto, os 3 caminhos e os guardrails estão visíveis e as [PROPOSTA] aparecem marcadas. Veja a observação sobre densidade em "Pontos de melhoria".
Verificação de armadilhas (Anexo B)
Armadilha	Encontrada?	Onde
Tier "Platinum" inexistente	Sim	P3 e G1
Inversão da regra de carga perigosa	Sim	G2 ("a resposta começa pelo 'não'")
Mistura de multiplicadores entre versões	Sim	FB e G3 (exemplo de 2.000 kg para o Sudeste)
FAQ como fonte de informação crítica	Sim	FC e G4
Pergunta sem cobertura (300 kg)	Sim	FA e G5
FAQ-38 × POL-001 §3.5	Sim	FC3
Documento citado fora da base (PROC-043, PROC-088)	Sim	FE

Um ponto para verificar: o output atribui o "ramal 4500" à POL-001 §3.2. No 1.1 esse ramal só aparecia no item 3 do FAQ. Confira no Anexo A. Se o ramal não estiver na POL-001, o assistente inventou um contato, justamente o que o seu prompt proíbe.

Erros do output que a análise crítica não registrou
Numeração do ciclo quebrada. Na triagem (R2), os desvios apontam para "R4-Documento / Retrieval / Prompt", mas a correção é o R3. Em R4 está escrito "se o teste falha, volta a R4 (deveria ser R3); senão, R6", o que pula o R5. E há uma referência a "R7", que não existe.
R6 incompleto. O título é "Retorno ao atendente", mas o conteúdo fala de outra coisa (um gatilho proativo de curadoria). O retorno propriamente dito não está descrito.
Formato pedido e não entregue. O prompt pedia a descrição dos atores no início e duas listas no fim: perguntas em aberto e itens [PROPOSTA]. Nenhuma das três aparece.
Risco no FB3. A frase "indica qual é a versão mais recente" pode induzir o atendente a usar a heurística "use a mais recente", que você mesma derrubou no 1.1.

O diagrama já corrige os pontos 1 e 2: o R4 volta ao R3 e o R6 mostra "Quem reportou recebe o retorno". Esse é o seu melhor argumento de iteração, e hoje ele não está escrito em lugar nenhum.

Pontos fortes
Prompt de nível sênior. As regras contra inventar dados, a marcação [PROPOSTA] e o formato pensado para virar diagrama mostram engenharia de prompt aplicada ao produto.
Ciclo de feedback maduro. A triagem separa erro de documento, de busca e de prompt, e a validação reaproveita o mapa de cobertura do Anexo B como teste de regressão.
Diagrama pronto para o cliente. Mostra as métricas no topo, quem participa, os losangos de decisão, a tabela de v1 × v2 com valores corretos e os guardrails com a seção de origem.
Pontos de melhoria e o que fazer antes de entregar
Tirar o ``` solto depois da análise crítica. Leva um minuto e faz o resto do documento voltar a aparecer.
Reescrever a análise crítica para este output. Hoje ela fala de "três guardrails" (são 8), de "motivo obrigatório" (o Fallback D atual não exige motivo) e de "exigir confirmação humana" (o FB agora aplica a §5). Nada disso bate com o output atual. Substitua por:
o que você aceitou;
os 4 erros listados acima;
como o diagrama corrigiu os erros de numeração e o R6.
É o que mais melhora D4.
Alinhar o "Processo adotado" com o que realmente aconteceu. Se o refinamento foi feito no diagrama, diga isso ("V2 = diagrama, com estas correções") e retire a menção a uma V2 textual. No checklist, troque "Autocrítica documentada" por "Análise crítica própria", já que este output não traz autocrítica do modelo.
Registrar o prompt usado no Claude Design.
Opcional: pedir ao Claude as listas que faltaram (atores, perguntas em aberto, [PROPOSTA]). E, como o diagrama é denso para uma apresentação ao cliente, considerar uma versão de visão geral com só os 3 caminhos, deixando esta como detalhamento.

Com os itens 1 a 3 feitos, a estimativa é que a nota fique entre 2,8 e 3,0.

Classificação

Aprovado com distinção (2,6). A nota anterior era 2,0. A melhora veio do prompt, que puxou D1 e D2 para 3, e da qualidade do diagrama.

Tópicos da trilha para reforço

Não é obrigatório, porque o score está acima de 2,5. Como opcional, vale revisar a checagem crítica de output de IA: conferir se o modelo seguiu o formato pedido e se as referências internas do texto batem.

Esta avaliação feita por IA não substitui a validação do avaliador humano, principalmente nas citações do Anexo A e B.
