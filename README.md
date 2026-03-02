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

> Observação: são realizados ajustes morfossintáticos para garantir concordância e adequação ao papel sintático de cada template. :contentReference[oaicite:9]{index=9}

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
##  Protocolo de avaliação: PSA (adaptado)

O protocolo de **Perturbation Sensitivity Analysis (PSA)** mede a sensibilidade do modelo à inserção do termo de grupo mantendo o restante constante. :contentReference[oaicite:11]{index=11}

### Pareamento x vs. xn

Para cada instância com termo (perturbada) `x_n`, constrói-se uma instância de controle `x` com o **mesmo template e a mesma configuração emocional**, diferindo apenas pela ausência do termo do grupo-alvo. :contentReference[oaicite:12]{index=12}

Formalização do pareamento: :contentReference[oaicite:13]{index=13}

- Seja `T` o conjunto de templates e `E` a configuração emocional (quando aplicável).
- Para cada `(t, e) ∈ T × E` e para cada termo `τ` do grupo `g`:
  - `x_n = render(t, e, τ)`
  - `x   = render(t, e, )`  *(remoção do termo no slot `<condição>`)*

---

### Escores por tarefa

O PSA requer uma função escalar `f(·)` que mapeia uma sentença para um valor contínuo. :contentReference[oaicite:14]{index=14}

**(a) Sentimento (BERTweet-PT)**  
\[
f_{sent}(x) = P(Pos) - P(Neg) \in [-1,1]
\]
Valores maiores indicam maior positividade. :contentReference[oaicite:15]{index=15}

**(b) Toxicidade (BERTimbau-PT)**  
\[
f_{tox1}(x) = P(Tóxico) \in [0,1]
\]
Valores maiores indicam maior probabilidade de toxicidade. :contentReference[oaicite:16]{index=16}

**(c) Toxicidade (ToxiGuardrail-PT)**  
O modelo retorna um logit \(\ell\) associado à segurança; padroniza-se para toxicidade via:
\[
f_{tox2}(x) = 1 - \sigma(\ell) \in [0,1]
\]
onde \(\sigma(\cdot)\) é a sigmoide. :contentReference[oaicite:17]{index=17}

---

### Métricas: Δ e ScoreSense

**Delta (variação por par):** :contentReference[oaicite:18]{index=18}  
\[
\Delta(x, x_n) = f(x_n) - f(x)
\]
Interpretação: quanto a inserção do termo altera a saída do modelo mantendo o restante constante. :contentReference[oaicite:19]{index=19}

**ScoreSense (efeito médio por grupo \(g\)):** :contentReference[oaicite:20]{index=20}  
\[
ScoreSense(g)=\frac{1}{|P_g|}\sum_{(x,x_n)\in P_g}\Delta(x,x_n)
\]
onde \(P_g\) é o conjunto de pares cujo termo instanciado pertence ao grupo \(g\). :contentReference[oaicite:21]{index=21}

---

### Significância estatística: t-test (H0: E[Δ] = 0)

Para verificar se as variações observadas são diferentes de zero, aplica-se teste por grupo com a hipótese nula: :contentReference[oaicite:22]{index=22}  
\[
H_0: \mathbb{E}[\Delta] = 0
\]
Na prática, utiliza-se t-test uniamostral sobre os deltas (equivalente ao pareado quando calculado sobre a diferença entre pares). :contentReference[oaicite:23]{index=23}

---

### Mudança decisória: LabelDist e FlipRate

Além do deslocamento contínuo, avalia-se impacto em **decisões discretas**, principalmente na tarefa de toxicidade.

**Função de decisão por limiar:** :contentReference[oaicite:24]{index=24}  
\[
d(x)=
\begin{cases}
1, & \text{se } f(x)\ge 0.5\\
0, & \text{caso contrário}
\end{cases}
\]

**LabelDist (distância de Jaccard entre conjuntos rotulados como tóxicos):** :contentReference[oaicite:25]{index=25}  
Sejam:
- \(A_g = \{ i \mid d(x_i)=1 \}\)
- \(B_g = \{ i \mid d(x_{n,i})=1 \}\)

\[
LabelDist(g)=1-\frac{|A_g\cap B_g|}{|A_g\cup B_g|}
\]
Se \(A_g\cup B_g=\varnothing\), define-se \(LabelDist(g)=0\). :contentReference[oaicite:26]{index=26}

**FlipRate (taxa de inversão de rótulo):** :contentReference[oaicite:27]{index=27}  
\[
FlipRate(g)=\frac{1}{|P_g|}\sum_{(x,x_n)\in P_g}\mathbb{I}[d(x)\ne d(x_n)]
\]
onde \(\mathbb{I}\) é a função indicadora.

---

##  Models Evaluated

### Sentiment
- `pysentimiento/bertweet-pt-sentiment`

### Toxicity
- `dougtrajano/toxic-comment-classification`
- `nicholasKluge/ToxiGuardrailPT`
- 
---

##  Referência
Esta metodologia é baseada na adaptação do BITS e do protocolo PSA para o contexto do português brasileiro, conforme descrito no manuscrito do projeto.

## 🧪 Reproducibility

All experiments are fully contained in
