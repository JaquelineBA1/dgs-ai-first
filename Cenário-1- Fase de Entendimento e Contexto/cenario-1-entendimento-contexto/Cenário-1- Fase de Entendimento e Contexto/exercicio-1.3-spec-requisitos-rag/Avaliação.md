Avaliação do Exercício 1.3 — Especificação de requisitos de RAG (ponto de vista de produto)

Papel: Product Specialist · Cenário: 1 — Entendimento e Contexto

Resumo

A V1 é forte: resolve contradições pela regra de transição (§5), trata separadamente cobertura parcial, documento fora da base e premissa falsa, e amarra cada teste às armadilhas do Anexo B. O feedback do Claude também é excelente. O problema está na V2: ela não incorpora os três pontos de gravidade alta do feedback e perde boa parte do que a V1 tinha de melhor. Além disso, traz dois critérios de teste com o resultado esperado errado. A iteração aconteceu, mas a versão final ficou mais fraca do que a primeira em contradições e em "sem resposta".

Scores por dimensão
Dimensão	Score	Justificativa
D1 — Domínio Conceitual	2	A V1 mostra que você entende as implicações do RAG para o produto: metadados de vigência, limite do conhecimento geral, cobertura parcial, rastreabilidade número a número. A V2 perde vários desses conceitos: SR-01, SR-03, SR-04 e SR-05, o registro de conflitos (CON-06) e a regra "nunca misturar versões num cálculo". Também passa a tratar a vigência do PROC-042 como "indefinida", ignorando a §5.
D2 — Uso de Ferramentas	2	O ciclo V1 → revisão → V2 está documentado e o feedback do Claude é de alta qualidade. Mas o prompt de revisão não diz por quais critérios revisar (e começa com "laude"). E a V2 não mostra o que foi aceito ou rejeitado do feedback.
D3 — Qualidade do Entregável	2	A V2 cobre as 5 áreas em tabelas, com código por requisito e um teste para cada um, o que é um bom formato. Porém tem contradições internas e testes com resultado esperado incorreto (detalhes abaixo). Um QA que seguir esses testes vai reprovar o comportamento correto.
D4 — Pensamento Crítico	1	A V2 contradiz conclusões suas do 1.1 e do 1.2 (sinistro, Platinum, a §5) e você não percebeu as regressões em relação à V1. Não há registro de decisão sobre o feedback.
D5 — Aplicabilidade ao Projeto	3	Todos os requisitos partem dos documentos e riscos reais da NovaTech, e o público-alvo (QA sem conhecimento de arquitetura) está bem definido.

Score do exercício: 2,0

Checklist da skill
Critério	Nota	Evidência
5 áreas cobertas	3	Fontes, contradições, ausência de resposta, atualização e rastreabilidade estão todas na V2.
Requisitos testáveis	2	Cada requisito tem um teste. Mas dois testes têm o resultado esperado errado, o RF5.2 depende de "confirmar visualmente… ou um resumo fiel" (subjetivo) e o teste do RF1.4 é circular.
Tratamento de contradições maduro	2	"Mostrar as duas com aviso" é uma solução aceita pela rubrica, mas a V2 regride: ignora a regra de transição por data do chamado, que a V1 e o seu 1.2 (G3) já aplicavam. O RF2.4 usa a §5, enquanto o RF1.3 e o RF2.1 a ignoram.
Iteração real	2	A V2 é diferente da V1 e tem melhorias que dá para verificar (prazo de atualização, testes por requisito, numeração padronizada, fim das referências soltas). Mas os pontos principais do feedback ficaram de fora, e houve perdas.
O feedback do Claude foi incorporado?
Ponto do feedback	Gravidade	Na V2
O item 1 exclui os documentos que o item 2 usa	Alta	Não. O problema continua: o RF1.1 exige "responsável formal", o FAQ não tem, e mesmo assim o RF1.2 manda indexá-lo. O mesmo vale para o PROC-042, que não tem classificação e seria barrado pelo RF1.1.
Tabela fonte → decisão → motivo (SharePoint, Confluence, planilhas, tabela mensal de fretes, PROC-043/088)	Alta	Não. Nenhuma dessas fontes é decidida.
Requisito de atualização sem prazo	Alta	Sim. O RF4.1 propõe 24h úteis, marcadas como proposta, e tem teste. A retirada de documentos não tem prazo, e o RF1.5 pode até bloqueá-la.
Seção própria para curadoria	Alta	Não. A palavra "curadoria" desapareceu, e o registro de conflitos (CON-06) foi retirado.
Especificar a regra de transição (perguntar a data do chamado)	Média	Regrediu. A V2 mostra as duas versões sempre.
Tema crítico em que só existe o FAQ (FAQ-32)	Média	Parcial. O RF3.3 põe o rótulo de informal, mas não bloqueia informação crítica.
Recusa indevida e registro de cada "não encontrei"	Média	Não.
Rastreabilidade: trecho literal e todo número presente no trecho	Média	Parcial. O trecho vira "ou um resumo fiel", e a regra do número no trecho saiu.
Referências soltas, numeração e parágrafo duplicado	Forma	Sim.
Erros de conteúdo na V2
RF1.2 e RF3.3 — Platinum. Os testes tratam a existência do tier "Platinum" como algo que só aparece no FAQ, e esperam o rótulo de fonte informal. Isso está errado: a SLA-2024 §1 é normativa e diz que só existem Gold, Silver e Standard. O comportamento correto é corrigir a premissa citando o SLA, como fazia o SR-05 da V1. É a armadilha 3 do Anexo B, e a V2 cai nela.
RF3.1 — Sinistro. O teste usa sinistro por carga danificada como exemplo de tema "ausente da base, sem política formal". Mas a POL-001 §3.5 trata avaria em trânsito, e o FAQ-38 está indexado. Isso contradiz o 1.1, o 1.2 e o próprio RF3.3. O exemplo certo de "sem resposta" era o da V1 ("300 kg para Salvador"), que saiu.
RF1.4 — PROC-043. A V2 trata o PROC-043 como indexado ("qualquer resposta que cite PROC-043…"). Na V1 (SR-04) e no seu 1.2 (Fallback E), ele é um documento fora da base.
RF1.3 — Vigência "indefinida". O §5 da v2 define qual versão vale pela data de abertura do chamado. Marcar a vigência como indefinida descarta uma regra que está documentada.
RF1.1 — Tipos de fonte. A lista de tipos passou a ser Política / Procedimento / SLA. Com isso some a classificação normativo / contratual / informal, que é o que sustenta a regra "o oficial prevalece sobre o FAQ".
Verificação de armadilhas (Anexo B)
Armadilha	V1	V2
1 — Mistura de versões num cálculo	Encontrada, com teste de 2.000 kg para o Sudeste	Parcial: o RF2.1 cobre só o multiplicador
3 — Tier Platinum inexistente	Encontrada (SR-05)	Errada (RF1.2 e RF3.3)
5 — Cobertura parcial (300 kg)	Encontrada (SR-03)	Perdida
Inversão da regra de carga perigosa	Não coberta	Não coberta
FAQ como fonte de informação crítica	Parcial	Parcial (RF3.3)
Pontos fortes
V1 com testes ancorados nas armadilhas. Cada requisito aponta o caso do Anexo B que o prova, e o SR-01 (limite do conhecimento geral, com o exemplo "considerando Manaus na região Norte") é um requisito de nível sênior.
Formato da V2 pronto para o QA. Tem código, requisito e teste em tabela, e o prazo proposto está marcado como proposta.
Pendências declaradas. O prazo de ingestão e a autoridade para marcar documento como obsoleto aparecem como decisões a validar, e não como fatos.
O que fazer antes de entregar (maior impacto com menos esforço primeiro)
Corrigir os dois testes errados. Platinum passa a corrigir a premissa com a SLA-2024 §1. O exemplo de "sem resposta" volta a ser "300 kg para Salvador", e o sinistro vai para contradições (POL-001 §3.5 × FAQ-38). Corrija também o PROC-043 como documento fora da base. É rápido e tira os erros de D3.
Fazer uma V3 que junte as duas versões: o formato em tabela da V2 com o conteúdo da V1 que se perdeu (SR-01 a SR-05, CON-06, "nunca misturar versões num cálculo", regra da §5 com a pergunta pela data do chamado). Ao mesmo tempo, trate os pontos de gravidade alta do feedback:
resolver o conflito RF1.1 × RF1.2 com metadados padrão preenchidos pela curadoria;
criar a tabela de fontes (fonte, decisão, motivo);
criar a seção de curadoria;
acrescentar requisitos para recusa indevida, registro de cada "não encontrei", trecho literal e número presente no trecho.
Registrar a análise do feedback: uma tabela com cada ponto do Claude, "aceito / rejeito / adaptado" e o motivo. É o que mais melhora D4 e o critério de iteração.
Melhorar o prompt de revisão, dizendo os critérios: os 5 pontos do enunciado, testabilidade e coerência com os Anexos A e B.

Com os itens 1 a 3 feitos, a nota provavelmente fica entre 2,6 e 2,8.

Classificação

Aprovado (2,0). A V1 tinha nível de distinção. A nota cai porque a versão final perdeu esse conteúdo e trouxe erros.

Tópicos da trilha para reforço
RAG: implicações de produto da vigência documental e da cobertura parcial.
Engenharia de Prompt: iteração em que a V2 incorpora o feedback de forma verificável, sem regredir.
Revisão crítica: conferir os testes gerados contra o corpus antes de aceitar.

Esta avaliação feita por IA não substitui a validação do avaliador humano, principalmente nas citações dos Anexos A e B.

Se quiser, monto a V3 juntando o que havia de bom na V1 com o formato da V2.
