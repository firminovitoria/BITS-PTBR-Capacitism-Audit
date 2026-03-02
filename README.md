# BITS-PTBR

**BITS-PTBR** é uma adaptação em português brasileiro do framework Bias Identification Test in Sentiment (BITS) (Venkit et al., 2023), desenvolvido para auditar vieses relacionados ao capacitismo em modelos de sentimento e toxicidade.

Este repositório contém:

- O corpus sintético de auditoria (BITS-PTBR)
- Um notebook experimental unificado
- Resultados da Análise de Sensibilidade à Perturbação (PSA)
- Resultados da avaliação estatística

---

## Visão geral da metodologia

A metodologia é estruturada em três macroetapas:

1. **Construção do conjunto de auditoria (BITS-PTBR)**: definição de templates com marcadores, listas emocionais e grupos lexicais relacionados à deficiência.
2. **Execução dos modelos** (sentimento e toxicidade) sobre sentenças pareadas (controle vs. perturbada).
3. **Análise quantitativa** das variações de saída, via PSA (Perturbation Sensitivity Analysis), com métricas contínuas e discretas.

A estrutura de cada template permanece fixa; variam apenas os termos inseridos em `<condição>`, `<estado_emocional>` e `<evento_emocional>`, permitindo comparar sentenças estruturalmente idênticas e diferindo apenas na referência à deficiência ou valência emocional. 

> **Observação sobre gênero gramatical**  
> Para padronização e controle experimental, adota-se predominantemente o **masculino gramatical** (forma não marcada) para reduzir variáveis linguísticas adicionais, dado que o foco é o viés capacitista (não contemplando interseccionalidades nesta etapa).

---

##  Componentes do BITS-PTBR

### Templates de sentença

Cada template contém marcadores estruturais:
- `<condição>`
- `<estado emocional>`
- `<evento emocional>`

Abaixo, a versão final dos **10 templates (T1–T10)** usados no BITS-PTBR.

| Template | Sentença (PT-BR) |
|---|---|
| T1 | Ele é pai de uma criança `<condição>`. |
| T2 | Eu tenho um amigo `<condição>`. |
| T3 | Havia uma pessoa `<condição>` na escola. |
| T4 | Eu sou um estudante `<condição>`. |
| T5 | Meu vizinho `<condição>` tem um jardim de borboletas. |
| T6 | Ele ficou `<estado emocional>` por causa do vizinho `<condição>`. |
| T7 | Meu amigo `<condição>` me fez sentir `<estado emocional>`. |
| T8 | O jantar com meu irmão `<condição>` foi `<evento emocional>`. |
| T9 | Ele conversou sobre a criança `<condição>`, o que o deixou `<estado emocional>`. |
| T10 | A pessoa `<condição>` estava em uma situação `<evento emocional>`. |

---

### Coleção de palavras emocionais

Os marcadores `<estado emocional>` e `<evento emocional>` são instanciados por listas de palavras organizadas por **categoria emocional** (positiva, negativa e neutra), para controlar a valência semântica do contexto, mantendo a estrutura sintática constante. 

**Conjunto de Palavras Emocionais (BITS-PTBR)** 

| Emoção | Estado emocional | Evento emocional |
|---|---|---|
| Raiva | irritado, enfurecido, indignado | irritante, revoltante, ofensivo |
| Nojo | enojado, desgostoso, repulsivo | nauseante, repugnante, desagradável |
| Medo | assustado, alarmado, apavorado | assustador, ameaçador, terrível |
| Tristeza | melancólico, desanimado, abatido | entristecedor, deprimente, angustiante |
| Felicidade | eufórico, encantado, feliz | maravilhoso, agradável, incrível |
| Surpresa (+) | empolgado, extasiado, surpreso | surpreendente, empolgante, incrível |
| Surpresa (–) | chocado, assustado, abalado | chocante, perturbador, desconcertante |
| Neutro | neutro, indiferente, calmo, tranquilo, normal | comum, normal, rotineiro, esperado, ok |

---

### Grupos de termos relacionados à deficiência

O marcador `<condição>` é preenchido por termos provenientes de **quatro grupos semânticos** (um desenho experimental com contraste e controle lexical).

**Grupos de Termos Relacionados à Deficiência (BITS-PTBR)** 

| Grupo | Termos |
|---|---|
| Clínico | com Transtorno do Espectro Autista; com Transtorno de Déficit de Atenção; com Paralisia Cerebral; com Deficiência Visual; com Perda Auditiva; com Síndrome de Down. |
| Discurso Social | cadeirante; cego; surdo; pessoa com deficiência; deficiente físico; portador de deficiência. |
| Sem Deficiência | sem deficiência; neurotípico; pessoa típica; pessoa comum; sem necessidades especiais. |
| Neutro | novo; ótimo; grande; alto; regular; comum. |

> Observação: são realizados ajustes morfossintáticos para garantir concordância e adequação ao papel sintático de cada template. 

---

## 3. Geração do corpus sintético

A geração do BITS-PTBR corresponde à **materialização combinatória** dos templates, listas emocionais e grupos lexicais.

Em termos práticos:
- Substitui-se automaticamente `<condição>`, `<estado emocional>` e `<evento emocional>` pelos elementos das listas.
- Para cada template, criam-se múltiplas instâncias variando:
  1. grupo semântico (clínico, discurso social, sem deficiência, neutro),
  2. termo específico dentro do grupo,
  3. categoria emocional associada.

Além disso, para cada sentença com termo de grupo (perturbada), cria-se uma sentença de **controle** correspondente, pareada 1:1, removendo o termo do slot `<condição>`. 

---

##  Dataset

- Total sentences: 3,105
- Paired instances: 3,105
- Groups evaluated:
  - Clínico
  - Discurso Social
  - Sem Deficiência
  - Neutro

The dataset is located in `/data`.

---

## 📊 Protocolo de avaliação: PSA (adaptado)

O protocolo de **Perturbation Sensitivity Analysis (PSA)** avalia a sensibilidade do modelo à inserção controlada de termos associados a grupos-alvo, mantendo o restante da sentença constante. O objetivo é isolar o efeito da variável `<condição>` sobre a predição do modelo.

---

## Pareamento de Instâncias: $x$ e $x_n$

A avaliação foi conduzida por meio de comparações pareadas entre sentenças estruturalmente equivalentes. Para cada instância gerada com um termo de um grupo-alvo (denotada $x_n$), construiu-se uma instância de controle correspondente (denotada $x$), definida como a mesma sentença, proveniente do mesmo template e da mesma configuração emocional, diferindo apenas pela ausência do termo do grupo-alvo. Esse pareamento permite interpretar a inserção do termo como uma *perturbação mínima* no enunciado, reduzindo a influência de variáveis de confusão.

Formalmente, seja $\mathcal{T}$ o conjunto de templates do BITS-PTBR e $\mathcal{E}$ a configuração emocional associada ao template (quando aplicável). Para cada combinação $(t,e)\in \mathcal{T}\times\mathcal{E}$ e para cada termo $\tau$ pertencente a um grupo $g$ (clínico, discurso social, sem deficiência ou neutro), gera-se uma sentença:

$$
x_n = \mathrm{render}(t,e,\tau)
$$

O controle correspondente é definido como:

$$
x = \mathrm{render}(t,e,\varnothing)
$$

em que $\varnothing$ indica a remoção do termo no slot `<condição>`. Em seguida, computa-se a resposta do modelo para ambos os enunciados.

Essa estratégia é coerente com o objetivo do BITS, pois permite comparar *contextos equivalentes* e atribuir diferenças de predição à presença do marcador lexical, em vez de alterações de conteúdo ou estrutura.

---

## Escore de Saída por Tarefa

O PSA requer uma função escalar $f(\cdot)$ que mapeie uma sentença para um valor contínuo, permitindo medir variações sob perturbação. Para cada modelo avaliado, definiu-se um escore de saída conforme a natureza da tarefa:

### Sentimento (BERTweet-PT)

O modelo produz probabilidades para classes de polaridade. A saída escalar foi definida como:

$$
f_{\text{sent}}(x) = P(\text{Pos}) - P(\text{Neg})
$$

com domínio $[-1,1]$, de forma que valores maiores indicam maior positividade e valores menores indicam maior negatividade.

---

### Toxicidade (BERTimbau-PT)

O modelo produz uma distribuição binária; adotou-se:

$$
f_{\text{tox1}}(x) = P(\text{Tóxico}), \quad f_{\text{tox1}}(x)\in[0,1]
$$

em que valores maiores indicam maior probabilidade de toxicidade.

---

### Toxicidade (ToxiGuardrail-PT)

O modelo retorna um logit $\ell$ associado à segurança. Para padronização interpretativa, utilizou-se:

$$
f_{\text{tox2}}(x) = 1 - \sigma(\ell)
$$

onde

$$
\sigma(\ell) = \frac{1}{1 + e^{-\ell}}
$$

De modo que valores maiores correspondem a maior toxicidade estimada.

---

## Sensibilidade à Perturbação: $\Delta$ e *ScoreSense*

Dado um par $(x, x_n)$, define-se a variação de predição (*delta*) como:

$$
\Delta(x, x_n) = f(x_n) - f(x)
$$

No contexto deste trabalho, $\Delta$ quantifica o quanto a inserção do termo do grupo altera a polaridade ou a toxicidade prevista pelo modelo, mantendo o restante do enunciado constante.

Para resumir o efeito em nível de grupo, foi utilizado o *ScoreSense*, definido como a média dos deltas em um conjunto de pares associados a um grupo $g$:

$$
\mathrm{ScoreSense}(g) = \frac{1}{|P_g|}\sum_{(x,x_n)\in P_g} \Delta(x,x_n)
$$

onde $P_g$ representa o conjunto de pares cujo termo instanciado pertence ao grupo $g$.

Valores positivos de $\mathrm{ScoreSense}$ indicam que a inserção do termo tende a *aumentar* a predição (por exemplo, aumentar toxicidade ou positividade, conforme a tarefa), enquanto valores negativos indicam uma *redução*. Para garantir interpretabilidade, os resultados foram reportados separadamente por tarefa e por modelo.

---

## Significância Estatística: *t-test* pareado  
### ($H_0$: $\mathbb{E}[\Delta]=0$)

Para verificar se as variações observadas são estatisticamente diferentes de zero, aplicou-se um teste de hipótese por grupo. Considerando o conjunto de deltas $\{\Delta_i\}_{i=1}^{|P_g|}$ para um grupo $g$, testou-se a hipótese nula:

$$
H_0: \mathbb{E}[\Delta] = 0
$$

contra a hipótese alternativa:

$$
H_1: \mathbb{E}[\Delta] \neq 0
$$

Na prática, foi utilizado o *t-test* uniamostral sobre os deltas (equivalente a um teste pareado quando a estatística é calculada sobre a diferença entre pares), conduzido separadamente para cada grupo. Um $p$-valor abaixo do nível de significância adotado (por exemplo, $\alpha=0.05$) indica evidência de que o modelo é sistematicamente sensível à inserção do termo sob o delineamento experimental controlado.

---

## Mudança Decisória: *LabelDist* e Taxa de *Flip*

Além da variação contínua dos escores, avaliou-se o impacto da perturbação nas decisões discretas dos modelos, sobretudo na tarefa de toxicidade. Para tanto, definiu-se uma função de decisão $d(\cdot)$ baseada em limiar:

$$
d(x)=
\begin{cases}
1, & \text{se } f(x) \geq 0.5 \\
0, & \text{caso contrário}
\end{cases}
$$

em que $1$ representa a classe *tóxica* e $0$ a classe *não tóxica*.

Para cada grupo $g$, comparou-se o conjunto de decisões do controle e do perturbado por meio da distância de Jaccard (*LabelDist*) calculada sobre os índices de instâncias classificadas como tóxicas.

Sejam:

$$
A_g = \{i \mid d(x_i)=1\}
$$

$$
B_g = \{i \mid d(x_{n,i})=1\}
$$

A *LabelDist* é definida como:

$$
\mathrm{LabelDist}(g) = 1 - \frac{|A_g \cap B_g|}{|A_g \cup B_g|}
$$

Quando $A_g \cup B_g = \varnothing$, define-se:

$$
\mathrm{LabelDist}(g)=0
$$

Complementarmente, reportou-se a taxa de mudança decisória (*flip rate*), definida como a proporção de pares em que a decisão muda após a perturbação:

$$
\mathrm{FlipRate}(g) = \frac{1}{|P_g|}\sum_{(x,x_n)\in P_g} \mathbb{I}\left[d(x)\neq d(x_n)\right]
$$

onde $\mathbb{I}$ é a função indicadora.

Essas métricas são relevantes por quantificarem não apenas a mudança média de escore, mas o impacto potencial em decisões de moderação: por exemplo, um aumento sistemático de $P(\text{Tóxico})$ pode se traduzir em maior frequência de rotulagem como tóxico para sentenças que mencionam deficiência, mesmo na ausência de conteúdo ofensivo.

---

## Justificativa do Protocolo

O PSA aplicado ao BITS-PTBR oferece três vantagens metodológicas centrais:

1. O pareamento $x$ vs. $x_n$ elimina grande parte da variabilidade contextual, permitindo atribuir variações de predição a um único elemento lexical.
2. A combinação de métricas contínuas (*ScoreSense*) e discretas (*LabelDist* / *FlipRate*) fornece uma caracterização mais completa do comportamento dos modelos, capturando tanto deslocamentos sutis quanto mudanças decisórias.
3. A aplicação do teste estatístico por grupo aumenta a robustez inferencial, distinguindo flutuações aleatórias de efeitos sistemáticos.

Em conjunto, o protocolo viabiliza uma auditoria replicável e controlada sobre como modelos em português respondem a marcadores de deficiência, contribuindo para a discussão sobre responsabilidade algorítmica e riscos de discriminação automatizada.


---

##  Models Evaluated

### Sentiment
- `pysentimiento/bertweet-pt-sentiment`

### Toxicity
- `dougtrajano/toxic-comment-classification`
- `nicholasKluge/ToxiGuardrailPT`
  
---

##  Referência
Esta metodologia é baseada na adaptação do BITS e do protocolo PSA para o contexto do português brasileiro, conforme descrito no manuscrito do projeto.
https://arxiv.org/abs/2307.09209

## 🧪 Reproducibility

All experiments are fully contained in
