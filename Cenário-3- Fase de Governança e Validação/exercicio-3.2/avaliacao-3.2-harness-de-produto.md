# Avaliação do Exercício 3.2 — Harness de produto para melhoria contínua

> **Programa:** Trilha de Certificação AI First — DGS / DB1 Global Software
> **Papel:** Product Specialist · **Cenário:** 3 — Governança e Validação
> **Entregável avaliado:** `docs/harness-de-produto.md` (v1)
> **Base da avaliação:** skill `avaliacao-product-specialist.md` e prompt padrão de avaliação. A skill Foundation e o enunciado do exercício não foram fornecidos, então a escala segue o prompt e a checagem das referências aos cenários 1 e 2 considera só o que está citado no próprio entregável.

## Resumo

O entregável é forte e maduro. Cobre os quatro critérios da skill com profundidade e mostra que o produto evolui sem degradar. Os guardrails são tratados como invariantes em todas as camadas: métricas, triagem, suíte de regressão e HITL. Há duas falhas técnicas pontuais, no desenho dos asserts e no canário, e falta evidência do uso do Claude.

## Scores por Dimensão

| Dimensão | Score | Justificativa |
|---|---|---|
| D1 — Domínio Conceitual | 3 | Separa o HITL de mudança (H1–H10) do HITL de resposta (HITL-01 a 03). Trata o LLM como não determinístico, com 3 execuções e regra de 3/3 para guardrail. Prevê que uma mudança corrige um caso e quebra outro. A confiança sai do código, não do próprio modelo. |
| D2 — Uso de Ferramentas | 2 | O exercício espera uso do Claude, mas o entregável não traz prints, trechos de conversa nem o que foi aceito ou rejeitado da IA. O raciocínio tem qualidade, mas não dá para avaliar como a ferramenta foi usada. |
| D3 — Qualidade do Entregável | 3 | Completo e aplicável: triagem com 7 causas e dono, suíte C1–C4 com critério de aprovação, camadas por tipo de mudança, template de PR, schema do golden set e o mínimo para a demo. Há um bug no exemplo de assert (ver melhorias), mas ele não compromete o desenho. |
| D4 — Pensamento Crítico | 3 | Decisões com julgamento próprio: "falha vira teste antes de virar correção" (o teste precisa falhar primeiro); remover uma golden query exige aprovação, para ninguém passar apagando o teste; a classe X rejeita pedido contra guardrail; tema sensível nunca aprova por decurso de prazo; aprovação humana não passa por cima de C1 reprovada. |
| D5 — Aplicabilidade ao Projeto | 3 | Muito conectado ao NovaTech: ADR-0001/0002/0004, `AGENTS.md` (AG-*), INC-1 e INC-3, PROC-042 v1×v2, FAQ-38 × POL-001 §3.5, casos #1 a #6 do 3.1, o módulo de feedback do Copilot que vazou dados e o prazo da demo. |

**Score do exercício: 2,8**

## Verificação de Armadilhas

O 3.2 não tem armadilhas obrigatórias. Verificação dos quatro critérios da skill:

| Critério | Resultado |
|---|---|
| Processo de feedback completo | ✅ Vai do atendente até a notificação de volta. O item só fecha quando a golden query passa em produção. |
| Regression testing | ✅ C1–C4, com a C1 dedicada aos guardrails e com poder de bloqueio. Inclui a regressão semanal contra a produção para detectar deriva. |
| Ponto de HITL | ✅ H1–H10 por nível de risco, com papéis definidos. Os nomes da NovaTech ficaram como pendência, o que é aceitável neste nível. |
| Preserva guardrails do cenário 2 | ✅ Princípio 1, camada C1 e tabela 3.3 mapeando cada ID de guardrail para uma golden query. |

## Pontos Fortes

1. **A camada C1 é determinística e bloqueia de fato.** Os guardrails ficam protegidos por schema, validador e asserts, não por um LLM avaliador. A aprovação humana não passa por cima dela.
2. **A triagem por causa (D1/D2/R/P/G/N/X) aponta a camada a corrigir e o dono.** Isso evita o reflexo de "mexer no prompt" para tudo. A classe G, em que o guardrail está errado, e a classe X, em que o pedido é contra um guardrail, mostram maturidade.
3. **Pragmatismo na seção 8.** Define o mínimo para a demo em 2 semanas e deixa o resto para depois do go-live, conforme a calibração do cenário 3.

## Pontos de Melhoria

1. **O assert de exemplo reprova a resposta correta.** Na GQ-C1-001, `must_not_contain: "pode ser devolvida pelo processo padrão"` também casa com a resposta certa, "**não** pode ser devolvida pelo processo padrão". Busca por substring não entende negação. *Ação:* checar um campo estruturado da resposta (ex.: `allowed: false`, `escalation_channel: "4500"`) ou usar padrões ancorados. Vale revisar todos os asserts da tabela 3.3 com essa lente.
2. **O canário tem pouco volume para decidir.** Com 5 atendentes durante 3 dias, a M7 ("Não útil" + 3 p.p.) não tem amostra suficiente para ser significativa. *Ação:* deixar M2 e M3 como critérios de rollback, que são eventos binários, e usar M7 só como sinal. Ou definir um volume mínimo de consultas antes de promover a mudança.
3. **Falta uma política para quando um documento válido reprova na regressão.** Se a área publica a nova versão de uma norma e a C1 falha, bloquear mantém o assistente respondendo com a versão obsoleta. O rollback do índice tem o mesmo risco. *Ação:* definir o comportamento provisório para esse caso, por exemplo tirar o tema do ar como lacuna ou encaminhar via HITL-01 até corrigir, e registrar quem decide (H4/H5).

Ponto menor: registrar a conversa com o Claude, com o que foi aceito, rejeitado e por quê, elevaria a D2 para 3.

## Classificação

**Aprovado com distinção (2,8)**

## Tópicos da Trilha para Reforço

Nenhum obrigatório, porque o score ficou acima de 2,5. Como opcional: registrar evidências de uso da ferramenta e desenhar asserts robustos (dentro de Harness Engineering / Structured Outputs).
