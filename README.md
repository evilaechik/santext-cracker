# santext-cracker
 This repository contains a full implementation of a white-box attack on the SanText/SanText+ obfuscation mechanisms. Given only the *obfuscated index sequence* produced by the SanText system, the pipeline reconstructs a plausible mapping from indices to original wordswithout needing the defender’s vocabulary file or their secret random seed.

## What It Does

SanText uses differential privacy to replace sensitive words with plausible decoys sampled from a protected vocabulary (e.g. top-25k words), guided by embedding similarity. Because the resulting index-to-word mapping varies from run to run, direct decoding is impossible — unless one recovers the mapping probabilistically.

This repo:
- Builds a public proxy vocabulary using a corpus like Wikipedia
- Loads GloVe embeddings and computes distances
- Estimates the unknown privacy parameter ε using collision statistics
- Reconstructs a reverse-likelihood matrix based on exp(-ε · distance)
- Decodes each obfuscated index into the most likely word

## Key Components

| Step | Description |
|------|-------------|
| 0    | Tokenize public corpus and extract top `|Vᴘ|` words |
| 1    | Load pre-trained GloVe embeddings |
| 2    | Use FAISS to compute k-nearest neighbors |
| 3    | Estimate ε from self-collision rate |
| 4    | Build reverse-likelihood matrix `L[i | j]` |
| 5    | Greedy decoding of observed indices |
| 6    | (Optional) EM refinement with language model like GPT-2 |

## Installation

```bash
pip install numpy scipy scikit-learn faiss-cpu
