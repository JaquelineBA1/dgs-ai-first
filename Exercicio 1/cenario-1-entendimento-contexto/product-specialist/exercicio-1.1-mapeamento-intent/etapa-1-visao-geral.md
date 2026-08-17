# Etapa 1 — Visão geral (metadados apenas)

## Prompt enviado ao Claude

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

## Output obtido

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
```

## Observações

<!--Observações: o output já aponta corretamente, só com metadados, o risco mais crítico do caso (a duplicidade do PROC-042) e antecipa 3 dos 4 gaps reais listados no Anexo A — o que mostra que uma visão geral bem estruturada já vale muito antes de gastar orçamento de contexto com conteúdo completo. -->
