# BITS-PTBR

**BITS-PTBR** is a Brazilian Portuguese adaptation of the Bias Identification Test in Sentiment (BITS) framework (Venkit et al., 2023), designed to audit ableism-related bias in sentiment and toxicity models.

This repository contains:

- The synthetic audit corpus (BITS-PTBR)
- A unified experimental notebook
- Perturbation Sensitivity Analysis (PSA) results
- Statistical evaluation outputs

---

## 📚 Overview

The goal of this project is to evaluate whether Portuguese NLP models assign systematically different sentiment or toxicity scores to sentences that differ only by the presence of disability-related terms.

The evaluation follows a controlled perturbation setup:

x  = control sentence  
xₙ = sentence containing disability-related term  

Δ = f(xₙ) − f(x)

Metrics computed:

- ScoreSense
- t-test (H₀: E[Δ] = 0)
- LabelDist
- FlipRate

---

## 📊 Dataset

- Total sentences: 3,105
- Paired instances: 3,105
- Groups evaluated:
  - Clínico
  - Discurso Social
  - Sem Deficiência
  - Neutro

The dataset is located in `/data`.

---

## 🤖 Models Evaluated

### Sentiment
- `pysentimiento/bertweet-pt-sentiment`

### Toxicity
- `dougtrajano/toxic-comment-classification`
- `nicholasKluge/ToxiGuardrailPT`

---

## 🧪 Reproducibility

All experiments are fully contained in:
