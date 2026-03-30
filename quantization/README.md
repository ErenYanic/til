# Embedding Model Quantization

## Overview

Quantization reduces the precision of model weights and computations from floating-point (FP32) to lower-bit representations (INT8, NF4, BF16), decreasing memory footprint and latency whilst preserving semantic quality.

## Context

This benchmark was conducted to evaluate quantization techniques for embedding models used in [SEC-SemanticSearch](https://github.com/ErenYanic/SEC-SemanticSearch), a semantic search system over SEC filings.

## Benchmark Results

Experiments on `google/embeddinggemma-300m` using 33 test sentences comprising SEC filing excerpts and search queries:

| Variant | VRAM (MB) | VRAM Reduction | Inference Time (ms) | Semantic Similarity |
|---------|-----------|----------------|---------------------|---------------------|
| FP32    | 1177.7    | baseline       | 595.2 ± 73.2        | 1.000000 (baseline) |
| BF16    | 592.0     | 50%            | 774.8 ± 44.7        | 0.999926            |
| INT8    | 883.6     | 25%            | 872.1 ± 52.4        | 0.999040            |
| NF4     | 840.8     | 29%            | 447.1 ± 28.1        | 0.974997            |

Semantic similarity reported as mean cosine similarity vs FP32 reference embeddings.

## Application in SEC-SemanticSearch

BF16 quantization was selected for the SEC-SemanticSearch production deployment. Whilst the theoretical VRAM reduction is 50%, practical measurement in that system achieved 27.12% savings. Performance degradation remained below 0.0001 in cosine similarity, effectively preserving search quality without detectable information loss.

## Key Findings

- **BF16** achieves 50% VRAM reduction with 0.9999 semantic similarity in notebook. In practice, system-level overhead limits gains to approximately 27%, but inference quality remains unaffected.

- **INT8 quantization** achieves 25% VRAM reduction with 0.999 semantic similarity, an acceptable trade-off for moderate memory constraints.

- **NF4** delivers the fastest inference with the lowest memory footprint, but introduces measurable semantic drift (~2.5%), making it suitable only when speed is prioritised over embedding fidelity.

## Conclusion

For production embedding systems, BF16 offers an optimal balance between implementation complexity and practical benefits.
