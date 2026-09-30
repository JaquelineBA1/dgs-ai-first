
# **Prompt enviado ao Claude**

Claude quero que atue como Tech Lead e aponte ambiguidades. Ajuste.


# **Output obtido**
Vou revisar como Tech Lead, comparando o mockup com o requirements v1.1 e o recorte de domínio. Primeiro levanto as ambiguidades, depois ajusto o canvas.

Editou um arquivo, executou um comando · 1 nota
NovaTech Assistant — Resposta no Teams
Claude Design·Apenas você 

Também atualizo o HTML para o GitHub com a versão revisada.

Executou um comando, arquivos compartilhados

Revisei o mockup como Tech Lead e o problema principal era de fonte: em alguns cards, a citação não continha a afirmação. Isso falha na regra (a) dos VCs. Corrigi isso no canvas e no HTML para o GitHub, e acrescentei uma sexta tela com estados que a spec deixa em aberto.

Citações que não sustentavam a afirmação (corrigido)

VC-04: a frase "não há documento de frete até 500kg" citava a PROC-042 §1, mas o trecho não diz isso. Também tirei o aviso de valor base, porque ele dava a entender que a fórmula da PROC-042 vale para 300kg. A ausência agora aparece como "Resultado da busca", sem fonte.
VC-23: a parte do frete de volta citava a POL-001 §3.2 para algo que o trecho não diz. Virou uma lacuna declarada.
VC-10 e VC-03: o banner de divergência, o aviso de conteúdo informal e a nota sobre a transição da v2 não tinham fonte. Agora têm.

Ambiguidades da spec que ficaram marcadas no mockup (borda tracejada)

Como citar uma ausência: a C-02 exige fonte em toda resposta, mas o aviso de lacuna (O-06) e o pedido de esclarecimento (VC-16) não têm documento para citar.
Manaus → Norte: a tabela mostrava "Norte" como fato. Agora a linha diz "se Manaus = Norte", ligada à OQ-05.
A transição da v2 não resolve a contradição: a regra do §5 está na própria v2, que não diz que substitui a v1. Também não está definido se o assistente deve pedir a data de abertura do chamado ao atendente (OQ-01).
C-12 × C-03: a C-12 manda mostrar o normativo primeiro, e a C-03 manda não declarar versão vencedora. Quando o FAQ contradiz a POL-001, a ordem já é uma forma de hierarquia.
Conteúdo sem chunk: o encaminhamento ao Comercial (VC-02) aparece no mockup, mas pela C-01 o modelo não poderia mostrá-lo enquanto não houver chunk (OQ-19).
PROC-043: a C-13 permite citar o documento pelo nome, mas não há chunk que o traga. Não está claro se ele deve aparecer no VC-23.
Casos sem comportamento definido: a espera de até 30 s (C-16 / OQ-15), o contexto fora da janela de 3 turnos (VC-16) e a pergunta fora de escopo (VC-17) agora estão na tela 6.

Viabilidade no Teams (a validar com o time do bot)

Adaptive Cards não permitem chips coloridos no meio do texto. Troquei as citações por texto monoespaçado entre colchetes, que dá para fazer.
Não existe ação de copiar para a área de transferência, então removi o botão "Copiar resposta".
A tabela v1 × v2 depende do elemento Table (schema 1.5) e de card em largura total.
Continua faltando um selo para documento obsoleto (ADR-0003 / OQ-18). Deixei registrado, mas não desenhei.

Também tirei o ícone de telefone do encaminhamento ao Comercial, porque a base não traz contato para ele.
