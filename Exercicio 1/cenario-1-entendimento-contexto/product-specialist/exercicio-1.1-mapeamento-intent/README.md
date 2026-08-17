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
<!-- justificativa da decisão de contexto -->

**Prompt utilizado:**
```
<!-- <!-- Tenho 5 documentos da NovaTech (empresa de logística) que preciso analisar antes de 
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
   práticas dos atendentes. --> -->
```

**Output obtido:**
```
<!-- cole aqui a resposta do Claude -->
```

**Como a qualidade do output foi (genérica? específica?):**
<!-- sua análise -->

Ver também: [etapa-1-visao-geral.md](./etapa-1-visao-geral.md)

## Etapa 2 — Análise profunda (2 documentos selecionados)

**Documentos escolhidos e por quê:**
<!-- ex: PROC-042 v1 e v2, por serem as duas versões contraditórias identificadas na etapa 1 -->

**Prompt utilizado:**
```
<!-- prompt -->
```

**Output obtido:**
```
<!-- output -->
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
