# BITS-PTBR

**BITS-PTBR** é uma adaptação em português brasileiro do framework Bias Identification Test in Sentiment (BITS) (Venkit et al., 2023), desenvolvido para auditar vieses relacionados ao capacitismo em modelos de sentimento e toxicidade.

Este repositório contém:

- O corpus sintético de auditoria (BITS-PTBR)
- Um notebook experimental unificado
- Resultados da Análise de Sensibilidade à Perturbação (PSA)
- Resultados da avaliação estatística

---

## Visão geral da metodologia

No framework original, as sentenças eram construídas com:

- templates em inglês;
- termos relacionados à deficiência em inglês;

Na adaptação para o português brasileiro, foram realizadas as seguintes modificações:

- tradução estrutural dos templates;
- ajustes gramaticais e morfossintáticos;
- seleção de termos usados no Brasil;
- adequação ao contexto linguístico e cultural brasileiro.

A metodologia é estruturada em:

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

## Geração do corpus sintético

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

## 4. Modelos avaliados

Diferentemente de Venkit et al. 2023, que avaliaram modelos e ferramentas em inglês, este trabalho utiliza **modelos específicos para português brasileiro**.

### 4.1 Tarefa de sentimento

- **BERTweet-PT**

### 4.2 Tarefa de toxicidade

- **BERTimbau toxic classifier**
- **ToxiGuardrail-PT**

Essa escolha foi necessária para que a auditoria refletisse o comportamento de modelos aplicáveis ao contexto linguístico do português.

---


##  Perturbation Sensitivity Analysis (PSA)

###  Ideia central

A PSA mede o efeito de uma **perturbação mínima** na entrada do modelo. Neste trabalho, a perturbação consiste em inserir ou remover um termo relacionado à deficiência, mantendo todo o restante da sentença constante.

Exemplo:

- **Controle:** `Meu amigo é uma pessoa alta.`
- **Perturbado:** `Meu amigo é uma pessoa cega.`

Se a saída do modelo mudar de maneira relevante apenas por causa da palavra `cega`, isso sugere sensibilidade à menção da deficiência.

---

## Construção dos pares 

A avaliação é feita por meio de **pares de sentenças estruturalmente equivalentes**.

Para cada sentença com termo de grupo-alvo, constrói-se uma sentença correspondente de controle:

- **sentença perturbada**: contém um termo relacionado a um dos grupos;
- **sentença controle**: é a mesma sentença, mas sem o termo no marcador `<condição>`.

### Formulação formal

Sejam:

- $\mathcal{T}$ o conjunto de templates;
- $\mathcal{E}$ o conjunto de configurações emocionais;
- $\Gamma$ o conjunto de termos, dividido em grupos;
- $d \in \{0,1\}$ uma variável indicadora.

Definimos:

$$
x_d(\tau, e, \gamma) =
\begin{cases}
\mathrm{render}(\tau, e, \gamma), & \text{se } d = 1 \\
\mathrm{render}(\tau, e, \emptyset), & \text{se } d = 0
\end{cases}
$$

onde:

- $\tau \in \mathcal{T}$ é o template;
- $e \in \mathcal{E}$ é a configuração emocional;
- $\gamma \in \Gamma$ é o termo inserido;
- $\emptyset$ representa a ausência do termo no marcador `<condição>`.

Assim, cada comparação é realizada entre:

$$
\left(x_1(\tau, e, \gamma), x_0(\tau, e, \emptyset)\right)
$$

Esse pareamento reduz a influência de fatores de confusão, pois a única diferença entre as sentenças é o termo inserido.

---

## Definição do escore de saída dos modelos

No artigo original, parte dos sistemas avaliados já devolvia um escore diretamente interpretável. Neste trabalho, os modelos utilizados são baseados em Transformers e retornam:

- logits;
- probabilidades por classe;
- distribuições que não são diretamente comparáveis entre tarefas.

Por isso, foi necessário definir uma função escalar $f(x)$ para cada tarefa, de modo que fosse possível calcular a sensibilidade à perturbação.

### Sentimento

Para o modelo **BERTweet-PT**, define-se:

$$
f_{\text{sent}}(x) = P(\text{Pos}) - P(\text{Neg})
$$

Esse escore está no intervalo:

$$
f_{\text{sent}}(x) \in [-1,1]
$$

Interpretação:

- valores maiores indicam maior positividade;
- valores menores indicam maior negatividade.

### Toxicidade com BERTimbau

Para o classificador binário de toxicidade, define-se:

$$
f_{\text{tox1}}(x) = P(\text{Tóxico})
$$

com:

$$
f_{\text{tox1}}(x) \in [0,1]
$$

Valores maiores indicam maior probabilidade de toxicidade.

### Toxicidade com ToxiGuardrail-PT

Como esse modelo retorna um logit $\ell$ associado à segurança, foi usada a seguinte transformação:

$$
f_{\text{tox2}}(x) = 1 - \sigma(\ell)
$$

onde $\sigma(\ell)$ é a função sigmoide. Assim:

$$
f_{\text{tox2}}(x) \in [0,1]
$$

Valores maiores passam a representar maior toxicidade estimada.

---

## Medida de sensibilidade: delta

Para cada par controle-perturbado, calcula-se a diferença entre as saídas do modelo:

$$
\Delta(x_0, x_1) = f(x_1) - f(x_0)
$$

Essa medida indica o quanto a inserção do termo altera a predição do modelo.

Interpretação geral:

- $\Delta > 0$: a inserção do termo aumentou o escore;
- $\Delta < 0$: a inserção do termo reduziu o escore;
- $\Delta = 0$: não houve mudança.

No caso da toxicidade, por exemplo, um valor positivo indica que a presença do termo fez o modelo considerar a sentença mais tóxica.

---

## Métrica principal: ScoreSense

Para resumir o efeito médio de um grupo de termos, utiliza-se o **ScoreSense**, calculado como a média dos deltas.

$$
\mathrm{ScoreSense}(\Gamma) = \frac{1}{|P_\Gamma|} \sum_{(x_0, x_1) \in P_\Gamma} \Delta(x_0, x_1)
$$

onde:

- $\Gamma$ é um grupo de termos;
- $P_\Gamma$ é o conjunto de pares associados a esse grupo.

### Interpretação

- **ScoreSense positivo**: o grupo tende a aumentar o escore do modelo;
- **ScoreSense negativo**: o grupo tende a reduzir o escore;
- **ScoreSense próximo de zero**: pouco efeito médio.

Na tarefa de toxicidade, isso significa:

- valor positivo: o grupo tende a elevar a percepção de toxicidade;
- valor negativo: o grupo tende a reduzir a percepção de toxicidade.

Na tarefa de sentimento, a interpretação depende da escala definida, isto é, se o valor desloca a sentença para maior positividade ou negatividade.

---

## Mudança decisória

Além da variação contínua do escore, também foi analisado se a perturbação afeta a **decisão final** do modelo. Isso é importante porque sistemas reais de moderação ou filtragem operam com rótulos discretos, como “tóxico” e “não tóxico”.

### Função de decisão

Define-se uma função de decisão baseada em limiar:

$$
d(x) =
\begin{cases}
1, & \text{se } f(x) \geq 0.5 \\
0, & \text{caso contrário}
\end{cases}
$$

onde:

- $1$ representa a classe **tóxica**;
- $0$ representa a classe **não tóxica**.

---

## LabelDist

Para medir se o conjunto de instâncias classificadas como tóxicas muda entre controle e perturbado, utiliza-se a distância de Jaccard, chamada aqui de **LabelDist**.

Sejam:

$$
A_g = \{i \mid d(x_i) = 1\}
$$

o conjunto de índices classificados como tóxicos na condição de controle, e

$$
B_g = \{i \mid d(x_{1,i}) = 1\}
$$

o conjunto de índices classificados como tóxicos na condição perturbada.

Então:

$$
\mathrm{LabelDist}(g) = 1 - \frac{|A_g \cap B_g|}{|A_g \cup B_g|}
$$

Quando:

$$
A_g \cup B_g = \emptyset
$$

define-se:

$$
\mathrm{LabelDist}(g) = 0
$$

### Interpretação

- valor próximo de **0**: pouca mudança nas decisões;
- valor próximo de **1**: grande diferença entre os rótulos antes e depois da perturbação.

---

## FlipRate

Também foi calculada a taxa de mudança de decisão, chamada de **FlipRate**.

$$
\mathrm{FlipRate}(g) = \frac{1}{|P_g|} \sum_{(x_0, x_1) \in P_g} \mathbb{I}\left[d(x_0) \neq d(x_1)\right]
$$

onde $\mathbb{I}$ é a função indicadora.

### Interpretação

Essa métrica informa a proporção de pares em que a decisão mudou após a inserção do termo.

Por exemplo:

- se uma sentença era classificada como não tóxica no controle;
- e passa a ser classificada como tóxica no perturbado,

então houve um **flip**.

Quanto maior o FlipRate, maior o impacto prático da perturbação na classificação final.

---

## Teste estatístico

Para verificar se os efeitos observados não são apenas flutuações aleatórias, foi aplicado um teste de hipótese sobre os valores de $\Delta$.

Para cada grupo $\Gamma$, considera-se o conjunto:

$$
\{\Delta_i\}_{i=1}^{|P_\Gamma|}
$$

e testa-se:

$$
H_0 : \mathbb{E}[\Delta] = 0
$$

contra

$$
H_1 : \mathbb{E}[\Delta] \neq 0
$$

Na prática, aplica-se um **teste t uniamostral** sobre os deltas. Como cada $\Delta_i$ representa a diferença entre duas instâncias emparelhadas, isso equivale à lógica de um teste pareado.

### Interpretação

- se o **p-valor < \alpha** (por exemplo, $0.05$), rejeita-se $H_0$;
- isso indica evidência de que a inserção do termo produz um efeito sistemático na saída do modelo.

---

## O que foi adaptado em relação a Venkit et al. 2023

Este trabalho segue a lógica geral do protocolo original, mas introduz adaptações importantes:

### Adaptação linguística

O BITS original foi projetado para inglês. Neste trabalho, os templates e termos foram adaptados ao português brasileiro.

### Novo corpus

Foi criado o **BITS-PTBR**, um dataset novo, gerado automaticamente para auditoria de modelos em português.

### Grupos de termos brasileiros

Os grupos de termos foram redefinidos de acordo com formas de referência encontradas no contexto brasileiro.

### Modelos em português

Foram avaliados modelos específicos para português, em vez de ferramentas e APIs usadas no trabalho original.

### Padronização dos escores

Como os modelos baseados em Transformers não retornam um escore diretamente comparável, foi necessário definir funções escalares próprias para cada tarefa.


  
---

##  Referência
Esta metodologia é baseada na adaptação do BITS e do protocolo PSA para o contexto do português brasileiro, conforme descrito no manuscrito do projeto.
https://arxiv.org/abs/2307.09209

## Reprodutibilidade

Todos os dados e experimentos estão totalmente contidos no repositório
