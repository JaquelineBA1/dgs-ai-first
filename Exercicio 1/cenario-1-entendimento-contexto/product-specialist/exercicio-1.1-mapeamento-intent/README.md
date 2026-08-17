# Exercício 1.1 — Mapeamento de intent com engenharia de contexto

**Papel:** Product Specialist (Analista de Requisitos)
**Ferramenta utilizada:** Claude (chat)

## Contexto

<!-- Breve resumo do exercício: você vai pré-analisar a documentação da NovaTech
em 3 etapas progressivas (engenharia de contexto), antes de qualquer entrevista
com stakeholder, para gerar hipóteses e identificar riscos. -->

## Estratégia de contexto adotada

<!-- Se eu tivesse fornecido os cinco documentos completos de uma vez, a primeira leitura provavelmente teria pulado direto para as contradições de superfície mais óbvias (números diferentes, prazos diferentes) e teria dado menos peso a padrões estruturais que só aparecem quando você olha só para metadados — como a sobreposição de dono entre Comercial e Operações, ou o fato de duas versões do mesmo procedimento existirem como documentos catalogados separadamente em vez de um único histórico versionado. Isso só ficou visível na etapa 1 porque não havia conteúdo nenhum para distrair.

Testar hipóteses em vez de só descrevê-las. Cada etapa funcionou como um ciclo de hipótese → verificação. Na etapa 1 houve o levantamento da hipótese de que o FAQ provavelmente seria "política paralela" com práticas não respaldadas. Na etapa 3, isso deixou de ser hipótese e virou fato verificável: o item 45 mostrou uma mistura real de v1 e v2 acontecendo na prática, não apenas um risco teórico que tinha sido especulado. Se tivesse compartilhado o FAQ junto com tudo mais desde o início, essa confirmação teria menos força — seria só mais um dado entre muitos, e não a validação de uma hipótese que já tinha sido exposta para poder conferir depois.

Isolar variáveis para fazer um diff rigoroso. Ao compartilhar as duas versões da PROC-042 na etapa 2, sem os outros três documentos competindo por atenção, ficou possível fazer uma comparação linha a linha de verdade — pegar diferenças sutis como 1,2 vs. 1,3 ou 1,15 vs. 1,2, que são exatamente o tipo de coisa que se perde quando a atenção está espalhada por cinco documentos ao mesmo tempo. Essa mesma precisão foi o que permitiu, na etapa 3, reconhecer o item 45 como uma mistura exata de v1 e v2.

Esse desenho imita a dinâmica real de uma entrevista de Discovery, e ao mesmo tempo testa como um assistente de IA raciocina sob informação incremental — que é literalmente um dos riscos que estou mapeando (um assistente que mistura versões, ou que ancora em conclusões antes de ter o quadro completo). Numa discovery de verdade, documentos formais chegam primeiro e documentos informais como o FAQ aparecem depois, muitas vezes revelando que a prática real diverge do que está escrito. Estruturar a análise nessa mesma ordem serve tanto para me preparar para a entrevista quanto para observar, na prática, se as conclusões da etapa 1 se sustentaram, precisaram de ajuste, ou foram diretamente confirmadas — o que é, em si, uma forma de auditar a qualidade do próprio raciocínio incremental.. -->

## Etapa 1 — Visão geral (metadados apenas)

**Por que forneci a informação nessa ordem:**
<!-- Optei por apresentar primeiro apenas os metadados — título, versão, responsável e resumo de uma linha — porque essa camada de informação já é suficiente para revelar riscos estruturais e de governança que ficam menos visíveis quando o conteúdo completo dos documentos está disponível. Sobreposição de responsáveis entre diretorias, proliferação de versões catalogadas como documentos distintos, ausência de controle formal de versão e desequilíbrio de maturidade documental são padrões que aparecem no nível dos metadados, e queria testar se essa camada, sozinha, já seria capaz de gerar hipóteses de risco relevantes antes de qualquer leitura de conteúdo.

Essa ordem também teve a função de estabelecer uma linha de base de hipóteses a ser confirmada, refinada ou descartada nas etapas seguintes — em vez de partir direto para contradições de superfície (números, prazos, fórmulas), que só se tornam visíveis com o conteúdo completo e que tendem a capturar a atenção primeiro, deixando os padrões estruturais em segundo plano. Ao fixar as hipóteses antes de ver o conteúdo, ficou possível avaliar depois, de forma mais rigorosa, se a análise documental confirmava ou contrariava o que a estrutura dos metadados já sugeria. -->

**Prompt utilizado:**
```
<!-- Tenho 5 documentos da NovaTech (empresa de logística) que preciso analisar antes de 
qualquer entrevista de discovery. Vou te passar só os metadados de cada um por
enquanto — título, versão, responsável e um resumo de uma linha — sem o conteúdo
completo. 
Com base só nisso, me dê um mapa dos temas cobertos e hipóteses de gaps ou riscos 
que já seja possível mapear, antes de eu te mostrar o conteúdo completo.
1. POL-001 — Política de Devolução de Mercadorias, v3.1, responsável: Diretoria de
   Operações. Define regras e prazos para devolução de mercadorias.
2. PROC-042 — Procedimento de Cálculo de Frete Especial, v1.0, responsável:
   Diretoria Comercial. Define fórmula e multiplicadores para frete de cargas
   acima de 500kg.
3. PROC-042-v2 — Procedimento de Cálculo de Frete Especial (Revisado), v2.0,
   responsável: Diretoria Comercial. Mesma finalidade do documento anterior, mas
   revisado.
4. SLA-2024 — Tabela de SLA por Tipo de Cliente, v2024.1, responsável: Diretoria
   Comercial + Operações. Define prazos de resposta/resolução por tier de cliente.
5. FAQ-Atendimento — Perguntas Frequentes do Time de Suporte, versão não
   controlada, sem responsável formal. Documento colaborativo e informal com
   práticas dos atendentes. -->
```

**Output obtido:**
```
<!-- Com base só nos metadados, dá para montar um mapa preliminar e algumas hipóteses fortes de risco — vale destacar que são hipóteses a validar quando eu vir o conteúdo e, principalmente, na entrevista.

Mapa de temas cobertos

Os cinco documentos cobrem quatro áreas temáticas, mas de forma desigual: devolução de mercadorias (POL-001), precificação de frete especial (PROC-042 e sua revisão), compromissos de atendimento por tier de cliente (SLA-2024), e práticas informais de suporte (FAQ-Atendimento). Note que há uma sobreposição de dono entre Comercial e Operações (frete é Comercial, devolução é Operações, SLA é dos dois), o que já sinaliza que o processo ponta a ponta atravessa mais de uma diretoria sem que nenhum documento pareça ser o "dono" da jornada completa do cliente.

Hipóteses de gaps e riscos

Governança de versões: PROC-042 existe em duas versões formalmente distintas (v1.0 e v2.0) catalogadas como documentos separados, não como histórico de um único documento. Isso sugere hipótese de risco real: pode não haver processo claro de deprecação, e times diferentes (ou sistemas/planilhas antigas) podem ainda estar usando a v1.0 sem saber que foi revisada. Vale perguntar na entrevista o que motivou a revisão — erro de cálculo, reclamação de cliente, mudança de mercado — porque isso costuma revelar o problema real que gerou a v2.0.

Fronteira entre devolução e frete especial: POL-001 trata de devolução de mercadorias em geral, e PROC-042 trata de frete para cargas acima de 500kg, mas nenhum título sugere uma política de logística reversa para cargas pesadas — ou seja, o que acontece quando uma carga de 600kg precisa ser devolvida? Quem calcula o frete de retorno, e sob qual regra? Isso é um gap clássico de fronteira entre políticas de diretorias diferentes, e tende a aparecer como "zona cinzenta" que cada área resolve do seu jeito.

SLA sem lastro operacional visível: SLA-2024 promete prazos de resposta/resolução por tier, mas nenhum dos outros documentos parece amarrar explicitamente os prazos internos das operações (aprovação de devolução, recálculo de frete) a esses SLAs. Hipótese: o SLA pode ter sido definido pelo Comercial como promessa comercial sem validação plena da capacidade operacional de cumpri-lo — um risco comum quando o dono é uma junção de duas diretorias em vez de uma área única de processo.

Segmentação de cliente não documentada à parte: SLA-2024 depende de "tipo de cliente"/tier, mas não há, entre os cinco documentos, um documento que defina os critérios dessa segmentação. Pode existir em outro lugar (CRM, contrato) — vale confirmar, porque se não existir formalmente, o SLA pode estar sendo aplicado de forma subjetiva.

FAQ-Atendimento como "política paralela": este é provavelmente o ponto de maior risco do lote. É o único documento sem dono formal, sem controle de versão, colaborativo — ou seja, é onde moram as exceções, os atalhos e o conhecimento tácito do time de suporte. Hipótese forte: o FAQ provavelmente contradiz ou complementa POL-001 e SLA-2024 em pontos que a diretoria não sabe que existem — é comum esse tipo de documento carregar a "política real" usada no dia a dia, criada para preencher lacunas dos documentos formais. Isso é ouro para a discovery, mas também é o maior risco de compliance/inconsistência com o cliente.

Maturidade desigual entre documentos: POL-001 já está na v3.1 (bastante amadurecida), enquanto PROC-042 está apenas na v2.0 e o FAQ nem tem controle de versão. Isso sugere que a organização tem disciplina de gestão documental na área de Operações (retorno/devolução), mas menos disciplina em Comercial e nenhuma em Atendimento — um padrão que vale confirmar na entrevista, pois costuma refletir onde estão os processos mais maduros de verdade.

Perguntas sugeridas para a discovery

Vale perguntar diretamente: o que mudou entre PROC-042 v1.0 e v2.0, e a v1.0 ainda circula em algum lugar? Existe algum caso de devolução de carga >500kg, e qual documento rege o frete desse retorno? O SLA foi validado com quem executa (Operações) antes de publicado, e existe medição real de aderência a ele? Quanto do que está no FAQ diverge do que está em POL-001 ou SLA-2024 — e há casos em que o time de suporte segue o FAQ sabendo que contraria a política formal?

Quando você mandar o conteúdo completo, posso validar essas hipóteses uma a uma e mapear com mais precisão onde estão as inconsistências reais entre os documentos.-->
 -->
```

**Como a qualidade do output foi (genérica? específica?):**
<!-- A qualidade foi genérica, em alguns pontos tentou especificar mas devido a falta de informações, acabou mencionando informações genéricas -->

Ver também: [etapa-1-visao-geral.md](./etapa-1-visao-geral.md)

## Etapa 2 — Análise profunda (2 documentos selecionados)

**Documentos escolhidos e por quê:**
<!-- Escolhi os documentos PROC-042 v1 e v2, por serem as duas versões contraditórias identificadas na etapa 1.Devido ao fato da v2 ser uma evolução de v1, mas não conter o texto em sua totalidade, pode gerar conflito de entendimento. Por este motivo, gostaria de verificar como a IA irá se comportar.-->

**Prompt utilizado:**
```
<!--Aqui estão os conteúdos completos do PROC-042 v1.0 e do PROC-042-v2 (revisado). Identifique todas as inconsistências entre as duas versões — valores, prazos, fórmulas, qualquer coisa que difira — e avalie o risco de um assistente de IA misturar as duas versões numa mesma resposta. 
[

```markdown
# PROC-042 — Procedimento de Cálculo de Frete Especial

**Versão:** 1.0
**Data de emissão:** 03/03/2023
**Responsável:** Diretoria Comercial
**Status:** Este documento não possui indicação formal de vigência ou obsolescência no sistema da NovaTech. Coexiste com a versão PROC-042-v2.

## 1. Objetivo

Definir a fórmula e os parâmetros para cálculo de frete especial aplicável a cargas com peso acima de 500kg.

## 2. Fórmula de cálculo

O frete especial é calculado como:

Valor do frete = Valor base × Multiplicador regional × Fator de peso

Onde:
- Valor base = tarifa publicada na tabela mensal de fretes.
- Multiplicador regional = fator aplicado conforme a região de destino (seção 2.1).
- Fator de peso = 1.0 para cargas de 500kg a 1.000kg; 1.2 para cargas de 1.001kg a 3.000kg; 1.5 para cargas acima de 3.000kg.

### 2.1. Multiplicadores regionais

| Região | Multiplicador |
|--------|--------------|
| Sul | 1.2 |
| Sudeste | 1.0 |
| Centro-Oeste | 1.3 |
| Nordeste | 1.4 |
| Norte | 1.6 |

## 3. Prazo de entrega para frete especial

O prazo de entrega para frete especial é calculado como o prazo padrão da rota + 2 dias úteis adicionais para manuseio de carga pesada.

## 4. Condições especiais

- Cargas acima de 5.000kg requerem aprovação prévia do gerente de operações regional.
- Cargas perigosas com peso acima de 500kg seguem tabela específica (PROC-043: Frete de Cargas Perigosas).
- Descontos de volume (mais de 10 fretes especiais/mês para o mesmo cliente) devem ser negociados pelo Comercial e registrados em aditivo contratual.

# PROC-042-v2 — Procedimento de Cálculo de Frete Especial (Revisado)

**Versão:** 2.0
**Data de emissão:** 10/11/2023
**Responsável:** Diretoria Comercial
**Status:** Este documento não possui indicação formal de que substitui o PROC-042 v1. Ambos coexistem no SharePoint sem hierarquia clara.

## 1. Objetivo

Definir a fórmula e os parâmetros atualizados para cálculo de frete especial aplicável a cargas com peso acima de 500kg. Os multiplicadores foram revisados para refletir os custos operacionais atualizados de cada região.

## 2. Fórmula de cálculo

O frete especial é calculado como:

Valor do frete = Valor base × Multiplicador regional × Fator de peso

Onde:
- Valor base = tarifa publicada na tabela mensal de fretes.
- Multiplicador regional = fator aplicado conforme a região de destino (seção 2.1).
- Fator de peso = 1.0 para cargas de 500kg a 1.000kg; 1.15 para cargas de 1.001kg a 3.000kg; 1.4 para cargas acima de 3.000kg.

### 2.1. Multiplicadores regionais (atualizados em novembro/2023)

| Região | Multiplicador |
|--------|--------------|
| Sul | 1.3 |
| Sudeste | 1.1 |
| Centro-Oeste | 1.4 |
| Nordeste | 1.5 |
| Norte | 1.8 |

## 3. Prazo de entrega para frete especial

O prazo de entrega para frete especial é calculado como o prazo padrão da rota + 3 dias úteis adicionais para manuseio e roteirização de carga pesada (anteriormente era + 2 dias na versão anterior).

## 4. Condições especiais

- Cargas acima de 5.000kg requerem aprovação prévia do gerente de operações regional.
- Cargas perigosas com peso acima de 500kg seguem tabela específica (PROC-043: Frete de Cargas Perigosas). Nota: a PROC-043 está em processo de revisão pelo Compliance e pode sofrer alterações.
- Descontos de volume: a partir de 8 fretes especiais/mês para o mesmo cliente, aplicar desconto de 5% sobre o multiplicador regional. Acima de 15 fretes/mês, desconto de 10%. Descontos maiores requerem aprovação da Diretoria Comercial.

## 5. Disposições transitórias

Durante o período de transição, chamados abertos antes de 01/12/2023 que ainda estejam em processamento devem usar os multiplicadores da versão anterior (PROC-042 v1). Chamados novos a partir de 01/12/2023 devem usar os multiplicadores desta versão.] -->
```

**Output obtido:**
```
<!--Inconsistências identificadas entre PROC-042 v1.0 e v2.0
Item	v1.0 (03/03/2023)	v2.0 (10/11/2023)	Direção da mudança
Fator de peso — 1.001kg a 3.000kg	1,2	1,15	queda
Fator de peso — acima de 3.000kg	1,5	1,4	queda
Multiplicador regional — Sul	1,2	1,3	alta
Multiplicador regional — Sudeste	1,0	1,1	alta
Multiplicador regional — Centro-Oeste	1,3	1,4	alta
Multiplicador regional — Nordeste	1,4	1,5	alta
Multiplicador regional — Norte	1,6	1,8	alta
Prazo adicional para carga pesada	+2 dias úteis	+3 dias úteis	alta
Desconto por volume — gatilho	>10 fretes/mês, negociado caso a caso, via aditivo contratual	≥8 fretes/mês → 5% sobre o multiplicador regional; >15 fretes/mês → 10%	mudança de natureza (negociação → regra fixa), não só de número
Nota sobre PROC-043 (cargas perigosas)	Não existe	Adverte que a PROC-043 está em revisão pelo Compliance	só existe na v2
Regra de transição (seção 5)	Não existe	Chamados abertos antes de 01/12/2023 usam multiplicadores da v1; a partir dessa data, usam a v2	só existe na v2

Ficaram iguais entre as duas versões: a estrutura da fórmula (Valor base × Multiplicador regional × Fator de peso), as faixas de peso (500–1.000kg / 1.001–3.000kg / acima de 3.000kg — só os multiplicadores dentro delas mudaram), e a exigência de aprovação prévia para cargas acima de 5.000kg.

Risco de um assistente de IA misturar as duas versões

Eu classificaria esse risco como alto, e por motivos bem específicos ao conteúdo, não apenas porque "existem duas versões":

Os números são próximos o suficiente para não soarem um erro. 1,2 vs. 1,3, ou 1,4 vs. 1,5, são o tipo de diferença que passa despercebida numa resposta gerada — diferente de um erro grosseiro que o próprio texto entregaria como incoerente. Um assistente pode compor uma resposta plausível e internamente "sem contradições visíveis" mesmo pegando o multiplicador regional da v2 com o fator de peso da v1, por exemplo.

A direção da mudança não é uniforme. O multiplicador regional subiu na v2, mas o fator de peso caiu. Se o assistente usar uma heurística ingênua do tipo "a versão mais recente costuma ter valores mais altos, então valores mais altos = mais atual", ele vai acertar o multiplicador regional e errar o fator de peso (ou vice-versa) — produzindo um cálculo que nunca existiu em nenhuma das duas versões.

O desconto por volume não é só uma mudança de número, é uma mudança de mecanismo: de negociação caso a caso (v1) para regra percentual automática (v2). Misturar aqui é o pior cenário: por exemplo, aplicar o gatilho de 8 fretes/mês (v2) mas tratar como "precisa de aditivo contratual" (v1), ou aplicar desconto de 5% a partir de 10 fretes (mistura de limiar v1 com percentual v2) — nenhuma combinação híbrida corresponde a uma regra real da empresa.

O prazo de entrega (+2 vs. +3 dias) é uma promessa direta ao cliente. Um erro aqui vira risco de SLA descumprido ou de expectativa incorreta passada ao cliente — não é um erro só "interno".

O ponto mais crítico, porém, é a seção 5 da v2 (disposições transitórias). Ela informa que a resposta correta não depende apenas de "qual versão é a vigente", mas da data de abertura do chamado: chamados abertos antes de 01/12/2023 e ainda em processamento devem usar os multiplicadores da v1. Isso significa que mesmo um assistente que identificasse corretamente "a v2 é a mais nova, devo usar ela" ainda estaria errado para uma parcela dos casos reais — a v1 continua sendo a resposta certa em determinadas situações, indefinidamente, até que esses chamados antigos se encerrem (não há data-limite para isso). Nenhum dos dois documentos, isoladamente, resolve completamente a pergunta "qual multiplicador eu uso agora" sem essa terceira variável (data de abertura do chamado).

Some a isso o fato de que nenhum dos dois documentos declara formalmente que substitui o outro — ambos dizem textualmente que coexistem sem hierarquia clara no SharePoint. Isso é o cenário clássico de risco em base de conhecimento para IA (RAG): uma busca por "cálculo de frete especial" tende a recuperar as duas versões como igualmente relevantes (títulos quase idênticos, mesma estrutura, mesmo objetivo), e sem um sinal de metadado do tipo "vigente/obsoleto", o modelo não tem como discriminar sozinho — ele pode responder combinando trechos de ambas sem perceber, especialmente se a pergunta do usuário não mencionar a data do chamado.

Uma mitigação natural para quando vocês montarem o assistente de discovery/atendimento: tratar documentos conflitantes sem data de vigência explícita como uma condição bloqueante (o assistente deveria se recusar a responder com um número específico e sinalizar a ambiguidade, ou perguntar a data do chamado antes de calcular), em vez de silenciosamente escolher uma versão. Isso também é, em si, uma descoberta de processo: a NovaTech precisa de um dono formal de vigência documental antes de qualquer IA poder responder com segurança sobre esse cálculo. -->
```

Ver também: [etapa-2-analise-profunda.md](./etapa-2-analise-profunda.md)

## Etapa 3 — Cruzamento com o FAQ-Atendimento

**Prompt utilizado:**
```
<!-- prompt -->
```

**Output obtido:**
```
<!-- output -->
```

Ver também: [etapa-3-cruzamento-faq.md](./etapa-3-cruzamento-faq.md)

## Mapa de riscos (mínimo 2)

| # | Risco identificado | Como levar para o discovery humano |
|---|---------------------|--------------------------------------|
| 1 | | |
| 2 | | |

Ver também: [mapa-de-riscos.md](./mapa-de-riscos.md)

## Reflexão — progressivo vs. tudo de uma vez

<!-- O que teria acontecido se você tivesse colado os 5 documentos completos
de uma vez no primeiro prompt? Compare com o resultado da abordagem progressiva.
Relacione com orçamento de atenção e context rot. -->

## Entregável

- [x] Estratégia de contexto documentada (acima)
- [x] 3 prompts com outputs (arquivos separados desta pasta)
- [ ] Análise crítica de cada etapa
- [ ] Reflexão sobre progressive disclosure
- [ ] Mapa de riscos (mínimo 2 itens)
