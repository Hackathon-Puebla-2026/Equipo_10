# Early Detection of Leaks in Drinking Water Pipelines

**QAOAmigos — Team 10 | OQI Hackathon LATAM 2026**

> Applying quantum optimization (QUBO/QAOA) to find the optimal placement of pressure sensors in water distribution networks.

---

## Team Members

| Name | Institution | Contact |
|---|---|---|
| Raúl Sánchez Grande | UNAM | raul_sanchez@ciencias.unam.mx |
| Angel Rodrigo Quintal Vega | TecNM / ITM | le23080577@merida.tecnm.mx |
| Arturo García Zurita | CICESE | gzurita@cicese.edu.mx |

---

## Problem Statement

Nearly **40% of drinking water** is lost globally due to undetected pipeline leaks. This not only reduces the effective availability of drinking water, but also increases operational costs and intensifies pressure on limited freshwater resources — directly undermining **UN SDG 6: Clean Water and Sanitation**.

Early leak detection can be performed using **pressure sensors** strategically placed across the network. The key question is:

> *Given a limited number of sensors, where should they be placed to detect the most leaks?*

This is a **combinatorial optimization problem** — a natural fit for quantum computing.

---

## Approach

### 1. Data & Pressure Modeling

The dataset consists of pressure readings at 4 network nodes under nominal conditions and simulated leak scenarios. For each node, we compute the **pressure drop** with respect to the nominal value:

$$\Delta p_i = |p_i^\text{lead} - p_i^\text{nom}|$$

### 2. Covariance-Based Redundancy Matrix

To avoid placing sensors at nodes that provide redundant information, we build a **redundancy matrix** from the covariance of pressure drops:

$$R = \text{Cov}(\Delta p) - \text{diag}(\text{Cov}(\Delta p))$$

A PCA analysis confirms that most hydraulic information is concentrated in **two principal components**, justifying the placement of exactly 2 sensors.

### 3. Energy / Cost Function (QUBO)

We minimize the following energy function to find the optimal binary sensor configuration $x \in { 0,1 }^n$:

$$E(x) = -\alpha \sum_i s_i x_i + \gamma \sum_i x_i + \sum_{i,j} R_{ij} x_i x_j$$

| Term | Role |
|---|---|
| $-\alpha \sum_i s_i x_i$ | **Sensitivity reward** — favors nodes with large pressure drops |
| $\gamma \sum_i x_i$ | **Budget penalty** — penalizes using too many sensors |
| $\sum_{i,j} R_{ij} x_i x_j$ | **Redundancy penalty** — discourages correlated sensor pairs |

This is reformulated as a **QUBO matrix** $Q$ with:

$$Q_{ii} = \gamma - \alpha s_i, \qquad Q_{ij} = R_{ij} \; (i \neq j)$$

### 4. Classical Solver (Brute Force QUBO)

All $2^4 = 16$ binary configurations are evaluated exhaustively to find the global minimum — serving as the classical benchmark.

### 5. Quantum Solver (QAOA)

The QUBO is mapped to an **Ising Hamiltonian** and solved using the **Quantum Approximate Optimization Algorithm (QAOA)** via Qiskit:

- The cost operator is derived from the QUBO via `QuadraticProgram.to_ising()`
- An ansatz circuit is built with `qaoa_ansatz` for $p = 2$ layers
- Classical optimization is performed with `scipy.optimize.minimize` (BFGS)
- The optimal circuit is sampled with 8192 shots to extract the most probable bitstring

A **grid search** over hyperparameters $(\alpha, \gamma)$ benchmarks QAOA performance across multiple configurations.

---

## Key Result

> **QAOA ≈ QUBO** — Both methods converge to the same optimal sensor configuration, consistent with the PCA dimensional analysis. This validates the quantum approach on a small-scale instance.

For $n = 4$ nodes, classical solvers are naturally faster. The value of this work lies in demonstrating a **quantum-ready architecture** that scales favorably as network size grows beyond classical feasibility.

---

## Repository Structure

```
Equipo_10/
├── Leaks.ipynb          # Main notebook: data processing, QUBO, and QAOA
└── README.md
```

---

## Installation

```bash
pip install qiskit qiskit-optimization qiskit-algorithms qiskit-aer
pip install pylatexenc numpy pandas matplotlib scipy
```

---

## Usage

Open and run `Leaks.ipynb` sequentially. The notebook is divided into:

1. **Data loading & covariance analysis** — pressure drop computation and PCA
2. **QUBO brute-force solver** — classical benchmark
3. **QAOA solver** — quantum circuit construction, optimization, and sampling
4. **Grid search** — hyperparameter sweep over $(\alpha, \gamma)$

---

## References

1. United Nations. *Sustainable Development Goal 6: Clean Water and Sanitation.*
2. World Health Organization (WHO). *Global Water Report, 2024.*
3. Farhi, E., Goldstone, J., & Gutmann, S. (2014). *A Quantum Approximate Optimization Algorithm.* arXiv:1411.4028.
4. Qiskit Documentation — [qiskit.org](https://qiskit.org)
5. Pérez, R. et al. *Pressure-based leak detection in water distribution networks.*
6. Farley, M. & Trow, S. *Losses in Water Distribution Networks.* IWA Publishing, 2003.

---

## License

This project was developed for the **OQI Hackathon LATAM 2026** at BUAP. Academic and non-commercial use permitted with attribution.
