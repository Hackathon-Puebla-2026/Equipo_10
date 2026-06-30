## Early Detection of Leaks in Drinking Water Pipelines Using QUBO and QAOA

## Overview

This project explores the solution of a combinatorial optimization problem using a covariance matrix, Quadratic Unconstrained Binary Optimization (QUBO) formulation and the Quantum Approximate Optimization Algorithm (QAOA).

---

## Problem Description

Early leak detection in drinking water distribution networks can be performed using pressure sensors that record anomalous variations in the hydraulic network. However, installing sensors at every node is economically infeasible; therefore, the problem consists of identifying the minimum set of sensor locations that maximizes leak diagnosticability by selecting positions with high sensitivity to leak scenarios and low redundancy among them. 

Objective:
- Maximize Leak Diagnosability (Sensitivity): Identify and select network nodes that exhibit the most significant pressure drops across multiple simulated leak scenarios.

- Minimize Sensor Redundancy: Mathematically penalize the selection of similar nodes that provide overlapping hydraulic information.

- Optimize Economic Cost: Minimize the total number of installed sensors required to achieve full network coverage.

- Investigate the potential of quantum optimization methods for infrastructure optimization problems

---

## Dataset and Simulation

The pressure response of the hydraulic network is obtained from simulated leak scenarios. Each scenario generates pressure variations at different nodes, which are used to estimate sensor sensitivity and correlation between measurement locations.

The dataset is represented as a pressure variation matrix:

$$
\Delta P =
\begin{bmatrix}
\Delta P_1^1 & \Delta P_2^1 & ...\\
\Delta P_1^2 & \Delta P_2^2 & ...\\
\vdots
\end{bmatrix}
$$

where rows correspond to leak scenarios and columns correspond to network nodes.

--

## Initial Approach: Sensor Placement Using Covariance Matrix Analysis

As a first approach, we analyze the covariance matrix of the pressure measurements in the water distribution network to identify the most informative locations for sensor placement.

The covariance matrix is a square matrix giving the covariance between each pair of elements of a given random vector. This matrix allows us to quantify the correlation and variability between different measurement points in the system. By analyzing the differences in pressure behavior among nodes, we aim to determine the locations where sensors can provide the most relevant information for detecting potential leaks.

The main idea is to place sensors in regions where pressure variations are more distinguishable, improving the ability to identify anomalies caused by leaks while reducing the number of required sensors.

This approach provides an initial estimation of the optimal sensor distribution before applying the QUBO formulation and QAOA optimization methods.

---

## QUBO Model

The QUBO model is basis of the penalty and favors in each term

Variables:

$$
x_i =
\begin{cases}
1, & \text{if a sensor is placed at node } i\\
0, & \text{otherwise}
\end{cases}
$$

The objective function is:

$$
H(x)= - \alpha \sum_i S_{i} x_i + \beta \sum_{i,j}R_{ij}x_ix_j + \gamma \sum_i x_i, 
$$

where each term corrsponds to different penalties, the term with the weight $\alpha$
$$
H_{leak} = - \alpha \sum_i S_{i} x_i 
$$

reduces the energy if a sensor exists in the node, the term $S_i$ is the sensitivity of find a sensor, calculates by 
$$
S_i = | H_i^0 - \frac{1}{l} \sum_n H_i^l|
$$
where $H_i^0$ is nominal pressure in the node $i$ and $H_i^l$ is the pressure in the node $i$ and test $l$.

In the other hand, the term 
$$
H_{redundancy} = \beta \sum_{i,j}R_{ij}x_ix_j,
$$

have the information about the redundancy of the system. In this term $\beta$ is weight and the term the matrix of reduncance is calculated by 

$$
R_{ij} = \text{cov}(\Delta P_i, \Delta P_j) - \text{diag}(\text{cov}(\Delta P_i, \Delta P_j))
$$

Finally, the term with the weight $\gamma$ create a penalty for use of more sensors, 
$$
H_{sensor penalty} = \gamma \sum_i x_i.
$$

---

## Quantum Approach

The QUBO problem is mapped into an Ising Hamiltonian with the QUBO formulation

\[
H=\sum_i h_i Z_i+\sum_{i,j}J_{ij}Z_iZ_j
\]

Then QAOA is used to approximate the minimum energy state.

---

## Results


