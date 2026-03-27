# ICML Submission 2607 Rebuttal Supplementary Material

Singular Proxies for Adaptive Caching in Diffusion Language Models

## 1. Extended Discussion on Figure 1

Figure 1: Extended analysis of adjacent-step state similarities. We visualize the similarities across four features (input, value, singular proxy, and output) for representative layers (1, 12, 24, and 32) of LLaDA-8B-Instruct. The value proxy uncovers drift in the final FFN output more effectively than input states. Furthermore, the singular proxy's near-identical behavior to the value proxy suggests that a low-rank approximation effectively captures value-space drift. In the first layer, drift is confined to the most recently decoded tokens, resulting in an approximated diagonal structure; off-diagonal elements indicate non-strict left-to-right decoding patterns. In later layers, drift propagates to more tokens via self-attention. By the last layer (32rd layer), a lower-triangular pattern emerges, indicating that final output states fluctuate mainly among previously decoded tokens while more distant further tokens remain stable.
<iframe src="assets/sim_heatmap_1.pdf" width="100%" height="600px"></iframe>
<iframe src="assets/sim_heatmap_12.pdf" width="100%" height="600px"></iframe>
<iframe src="assets/sim_heatmap_24.pdf" width="100%" height="600px"></iframe>
<iframe src="assets/sim_heatmap_32.pdf" width="100%" height="600px"></iframe>


Figure 2: Magnitude of drift across layers and decoding steps. To quantify drift intensity, we plot the fraction of drifting tokens across various similarity thresholds (0.1–0.9). Middle layers exhibit higher drift magnitudes compared to early and late layers, aligning with our observations in Section 3.4 and motivating our layer-wise adaptive budget allocation. The relative stability of drift magnitude across decoding steps justifies the use of a step-invariant budget.
<iframe src="assets/sim_threshold_heatmap.pdf" width="100%" height="600px"></iframe>


## 2. Additional Comparison with dKV-Cache, Elastic-Cache, and d2Cache

Table 1: Performance comparison against KV cache baselines (dKV-Cache, Elastic-Cache, and d2Cache). Metrics include Tokens Per Second (TPS), Time To First Token (TTFT), and Accuracy (std. dev. in parentheses) on LLaDA-8B-Instruct and Dream-v0-Instruct-7B.

|  |  | LLaDA-8B-Instruct |  |  | Dream-v0-Instruct-7B |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- |
|  |  | TPS | TTFT | Accuracy | TPS | TTFT | Accuracy |
| GSM8K | Baseline | 29.67 | 28.0 | 78.62 (1.13) | 17.86 | 23.6 | 75.21 (1.19) |
|  | dKV-Cache | 56.97 | 28.9 | 78.01 (1.14) | 22.17 | 24.4 | 74.82 (1.20) |
|  | Elastic-Cache | 48.00 | 27.4 | 77.41 (1.15) | 35.77 | 23.9 | 74.86 (1.22) |
|  | d2Cache | 26.85 | 28.7 | 76.42 (1.17) | 28.92 | 24.9 | 78.70 (1.13) |
|  | Ours | 190.73 | 28.7 | 78.24 (1.14) | 45.97 | 24.5 | 77.56 (1.15) |
| MBPP | Baseline | 5.75 | 28.7 | 39.20 | 36.51 | 22.2 | 57.40 |
|  | dKV-Cache | 7.83 | 28.1 | 39.20 | 39.56 | 22.2 | 56.40 |
|  | Elastic-Cache | 16.25 | 29.2 | 34.80 | 85.60 | 22.1 | 52.80 |
|  | d2Cache | 25.08 | 28.2 | 39.60 | 41.87 | 21.6 | 57.60 |
|  | Ours | 46.12 | 28.7 | 39.00 | 114.85 | 22.6 | 57.60 |


## 3. Additional Ablation Study on Singular Proxy Rank

Table 2: Influence of singular proxy rank ($r$) on LLaDA-8B-Instruct (MBPP).
|  | TPS | TTFT | Accuracy |
| --- | --- | --- | --- |
| Baseline | 5.75 | 28.7 | 39.20 |
| Ours ($r=512$) | 42.60 | 28.6 | 39.00 |
| Ours ($r=256$) | 44.32 | 28.8 | 38.80 |
| Ours ($r=128$) * | 46.12 | 28.7 | 39.00 |
| Ours ($r=64$) | 47.29 | 27.9 | 38.80 |
| Ours ($r=32$) | 47.13 | 28.4 | 38.20 |

Table 3: Influence of singular proxy rank ($r$) on Dream-v0-Instruct-7B (GSM8K).
|  | TPS | TTFT | Accuracy |
| --- | --- | --- | --- |
| Baseline | 17.86 | 23.6 | 75.21 (1.19) |
| Ours ($r=128$) | 43.02 | 24.9 | 77.63 (1.15) |
| Ours ($r=64$) | 45.69 | 24.6 | 77.48 (1.15) |
| Ours ($r=32$) * | 45.97 | 24.5 | 77.56 (1.15) |
| Ours ($r=16$) | 46.03 | 24.3 | 76.35 (1.17) |
| Ours ($r=8$) | 45.87 | 24.4 | 75.19 (1.17) |


## 4. Sensitivity Analysis of Adaptive Budget Allocation

Figure 3: Visualization of four noisy configurations. We evaluate the robustness of our Adaptive Budget Allocation by shifting $l_p$ away from its optimal value across four noisy configurations as shown below.
<iframe src="assets/budget_ablation_LLaDA-8B-Instruct.pdf" width="100%" height="600px"></iframe>


Table 4: Impact of $l_p$ variations on LLaDA-8B-Instruct (GSM8K). Our primary configuration balances efficiency and accuracy optimally. Performance remains stable despite variations in $l_p$, demonstrating the robustness of our adaptive strategy
    
|  | Peak $\rho_p$ | Avg $\bar{\rho}$ | TPS | Accuracy |
| --- | --- | --- | --- | --- |
| Baseline | 100% | 100% | 29.67 | 78.62 (1.13) |
| Ours | 25% | 25% | 164.88 | 78.59 (1.13) |
| Ours | 16% | 16% | 190.06 | 75.65 (1.17) |
| Ours * | 25% | 16% | 190.73 | 78.24 (1.14) |
| Ours (Noisy 0) | 25% | 16% | 191.43 | 77.17 (1.14) |
| Ours (Noisy 1) | 25% | 16% | 189.32 | 77.77 (1.13) |
| Ours (Noisy 2) | 25% | 16% | 189.64 | 77.95 (1.16) |
| Ours (Noisy 3) | 25% | 16% | 190.18 | 76.41 (1.15) |


## 5. Empirical Validation of Theoretical Assumptions in Section 3

### 5.1 Bounded Drift Assumption (Theorem 3.1)

Figure 4: Empirical distribution of drift ratio $\lambda$. We measure $\lambda = \frac{\sum_{j}\alpha^{t+1}_{ij}\|v^{t+1}_{j} - v^{t}_{j}\|}{\|v^{t+1}_{i} - v^{t}_{i}\|}$ across layers and steps for LLaDA-8B-Instruct and Dream-v0-Instruct-7B. Although $\lambda$ varies across layers, it generally remains below 4, and decreases below 2 in later layers, supporting the bounded drift assumption.

<iframe src="assets/assumption_bound_LLaDA-8B-Instruct.pdf" width="100%" height="600px"></iframe>
<iframe src="assets/assumption_bound_Dream-v0-Instruct-7B.pdf" width="100%" height="600px"></iframe>


### 5.2 Bounded Norm Assumption (Theorem 3.1)

Figure 5: Norm distributions of $v$ and $h$. Visualizing $\|v\|$ and $\|h\|$ across decoding steps and layers confirms that state norms remain within stable, bounded ranges for both models.
<iframe src="assets/assumption_norm_LLaDA-8B-Instruct.pdf" width="100%" height="600px"></iframe> 
<iframe src="assets/assumption_norm_Dream-v0-Instruct-7B.pdf" width="100%" height="600px"></iframe> 

### 5.3 The Truncated Subspace Assumption (Theorem 3.4)

Figure 6: Validation of the Truncated Subspace Assumption ($h \in \mathcal{span}(V_r)$) via Projection Energy Ratio (PER). We calculate PER as $\frac{1}{N}\sum_{i=1}^{N}\frac{\|V_r V_r^T h_i\|^2}{\|h_i\|^2}$. A PER near 1 indicates that the subspace $\text{span}(V_r)$ effectively captures the energy of states $h$. For both models, PER exceeds 0.9 across all layers when $r >= 128$ (LLaDA) or $r >= 32$ (Dream), validating our subspace truncation approach.

<iframe src="assets/span_assumption.pdf" width="100%" height="600px"></iframe> 


### 5.4 Stable Attention Assumption (Theorem 3.1)
Figure 7: Empirical distribution of attention shift. We observe that $\delta_A = \sum_{j}|\alpha^{t+1}_{ij} - \alpha^{t}_{ij}|$ is generally small. A transient spike in $\delta_A$ occurs during the initial decoding step for LLaDA-8B; however, as shown in Table 5, this does not negatively impact final performance.
<iframe src="assets/assumption_attn_LLaDA-8B-Instruct.pdf" width="100%" height="600px"></iframe>
<iframe src="assets/assumption_attn_Dream-v0-Instruct-7B.pdf" width="100%" height="600px"></iframe>


Table 5: Impact of warm-up steps on LLaDA-8B (GSM8K). Disabling caching for the initial 2–4 steps (warm-up) to account for the $\delta_A$ spike yields no significant accuracy gain while reducing efficiency. We decide to omit warm-up steps in the final configuration.

|  | Warm-up Steps | TPS | TTFT | Accuracy |
| --- | --- | --- | --- | --- |
| Baseline | - | 29.67 | 28.0 | 78.62 (1.13) |
| Ours * | 0 | 190.73 | 28.7 | 78.24 (1.14) |
| Ours | 2 | 185.41 | 28.1 | 78.86 (1.14) |
| Ours | 4 | 177.41 | 28.0 | 78.69 (1.13) |


## 6. Throughput-Memory Trade-off Analysis
Figure 8: Efficiency-memory trade-off across varying batch sizes. We evaluate the relationship between throughput (TPS) and GPU memory consumption. As batch size increases, throughput improves due to enhanced hardware utilization and parallelism. However, this gain is balanced by a concurrent increase in memory footprint. 
<iframe src="assets/batch_size_tps_memory.pdf" width="100%" height="600px"></iframe>
