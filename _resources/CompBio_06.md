---
title: "Markvos models for biomolecule conformational transistions sampled from MD"
collection: resources
category: computational biophysics
order: 6
---

MD simulations allow us to characterize protein structure, dynamics and function in the all-atom and femto-second spatiotemporal resolution. However, interpreting data of high dimensionality, such as MD, to infer the inherent transitions between different metastable conformational states, can be tricky. **Markob state model** (MSM) provides a generally accepted state-of-the-art dynamics analysis method.

**Markovanity**: the basic assumption of the Markov property is that the probability of **future state** only depends on the present state, not the history:

$$
P(X_{t+\tau} \mid X_t) \;=\; P(X_{t+\tau} \mid X_t, X_{t-\tau}, X_{t-2\tau}, \dots)

\tag{1}
$$

**Assumption**: the MD data being used to construct the MSM should be sufficiently sampled to cover the conformational states and well-characterized by the pre-defined collective variables. 

**Goal of MSM**:
- explain the mechanisms of biomolecular processes in terms of transitions between functionally relevant and structually well-defined metastable states
- the lag time $\tau_{lag}$ derived from the transition matrix defines the time resolution. It needs to be shorter than the fastest dynamics of interest
- the conformational ensembles (defined by the states) represent spatial resolution. They need to be sufficient to account for the relevant microscopic steps of the functional motion.
- however, short $\tau_{lag}$  while improving time resolution have undesirable effect of shortening timescale of MSM, resulting in loss of Markovianity.

**Quality of MSM** can be optimized by employing the variational principle (as most existing strategies do), meaning the optimal MSM should produce the longest implied timescales representing the true dynamics.

# 1. The general flow of MSM
- **Featurization**: selection of suitable input coordinates
- **dimensionality reduction**: from the high dimensional feature space to a low dimensional space of collective variables. Methods usually being used: **PCA**, ****
- **geometrical clustering**: from the low dimensional data into microstates
- **dynamical clustering**: from the microstates into metastable conformational state
- **Evaluation**: quality of the resulting transition matrix


## 1.1 Featurization

Use a set of collective variables (CVs) to characterize sampled snapshots of protein conformations.

Most used features
    - **Backbone / sidechain torsions (φ, ψ, χ angles)**
    - **Inter-residue distances / contacts** (Cα–Cα distances, heavy-atom contacts, etc.)
    - **RMSD relative to reference structures** (less common nowadays)
    - **Custom collective variables** (distance between domains, number of H-bonds, etc.)
    
 _Rule of thumb_: Use features sensitive to slow processes (folding, domain motion). Distances + dihedrals are very popular.


## 1.2 Dimension reduction

Since we may use lots of CVs (high dimensionality) and some of them may be highly correlated (or even degenerated), dimension reduction techniques are usually used to project those high dimensionality to the lower dimensional space while preserving the kinetics.

Often used methods:
- **TICA**: time-lagged independent component analysis
- **PCA**: principle component analysis
- **VAMP/VAMP-2** score optimization: generalization of TICA


## 1.3 Geometry clustering

This is for assigning frames into discrete **microstates**. Clustering algorithms are often adopted:
- K-means
- K-medoids
- density-based clustering: such as DBSCAN, RDC

Usually, this step can still result in hundreds, if not thousands, of microstates.

## 1.4 Lump microstates into macrostates

The idea here is to group kinetically similar miscrostates into a few metastable states. For example, the **most probable path algorithm** (MPP):
- starting with clustered microstates, the method first calculate the transition matrix using a pre-defined $\tau_{lag}$ (say, 10 ns)
- If a self-transition probability of a given microstate is lower than a certain metastability criterion $Q_{min} \in (0,1]$, the microstate will be lumped with the state to which the transition probability is the highest.
- Repeat the above step for increasing $Q_{min}$, as the microstates slowly lump into bigger **macrostate**, we can then choose an appropriate $Q_{min}$ for an appropriate number of matastable states (usually 5 ~ 20 macrostates).


**The role of lag time $\tau_{lag}$**
The **lag time $\tau_{lag}$** is the time interval between observations that you use to estimate the transition probabilities between these states. For example, if τ = 10 ns, then your transition probability matrix describes the likelihood of going from state _i_ to state _j_ over 10 ns.
- **If $\tau_{lag}$ is too short**:
	- You get more transitions, better statistics, and higher temporal resolution, however,
    - Transitions appear non-Markovian (memory effects matter)
    - The model may underestimate relaxation timescales, and implied timescales won’t converge
    - You risk overfitting to fast fluctuations rather than capturing the slow conformational dynamics
- **If $\tau_{lag}$ is too long**:
    - The Markov property holds better (memory is lost)
    - But you lose temporal resolution: fine details of kinetics are blurred out, and fast transitions can be missed
    - Statistical uncertainty increases because you get fewer effective transitions in the trajectory

## 1.5 Transition matrix estimation

Goal: Estimate transition probabilities between states at lag time $\tau_{lag}$.

- **Most used methods**:
    - **Maximum likelihood estimation (MLE)** of transition matrix (standard in PyEMMA, MSMBuilder).
    - **Bayesian estimators** (Bayesian MSMs → give uncertainty estimates).
    - **Reversible estimators** (enforce detailed balance to improve statistics).

## 1.6 Model validation and dynamics analysis

Goal: Check Markovianity and choose $\tau_{lag}$; also extract transition paths.
- **Most used diagnostics**:
    - **Implied timescales (ITS) plots** → look for plateau.
    - **Chapman–Kolmogorov (CK) test** → compare MSM predictions to MD data.
    - **VAMP-2 score** → quantitative measure of model quality.

Goal: Extract physical insights from the MSM.
- **Common analyses** for transition paths between states:
	- **Stationary distribution (π)** → equilibrium populations of states.
	- **Mean first passage times (MFPTs)** → kinetics between states (e.g., folding time).
	- **Pathway analysis** (committor analysis, transition path theory) → main transition routes.


# 2. Biophysical implications of the transition matrix

Assuming after MSM construction detailed above, we finally get the transition matrix $T$. Each element $T_{ij}$ gives the transition probability of state $i$ to state $j$. This matrix is row-stochastic, or bound by the condition that the sum of any one row would give 1.

$$
T_{ij} \geq 0
$$
$$
\sum_{j} T_{ij} = 1
$$

**Diagonalization of the transition matrix** will give us eigenvalues ($\lambda$) and eigenvectors ($v$).
$$
Tv_{k} = \lambda_{k} v_{k}
$$
- The largest eigenvalue $\lambda_{1}$ = 1 (this property of the transition matrix is granted by the *Perron–Frobenius theorem*). and the corresponding eigenvector $v_{1}$ represents **the stationary distribution** or **the equilibrium probabilities** of states
- Other eigenvalues $\lambda_{2}, \lambda_{3}, \dots$ will be smaller than 1. and each eigenvector corresponds to a relaxation process in the system

The implied timescale of $\lambda_{k}$ can be calculated as:
$$
t_{k} = - \frac{\tau}{\ln(\lambda_{k})}
$$
This is the characteristic timescale over which the process associated with that eigenmode decays.

The **eigenvectors** describe the conformational processes (modes):
- Each eigenvector $v_{k}$ gives a set of weights over the states.
- Positive vs. negative components indicate which sets of conformations are being exchanged.
- For example:
	- $\lambda_{1}, v_{1}$ → equilibrium populations of conformations (Boltzmann-like distribution).
	- $\lambda_{2}, v_{2}$ → the slowest conformational change (e.g., open ↔ closed transition). e.g. between open and closed states of the protein.
	- $\lambda_{3}, v_{3}$ → the next-slowest process (maybe flipping of a loop, or rotation of a domain).
	- Higher eigenvalues (closer to zero) → fast processes (side-chain rotamers, local motions).

For eigen pair $\lambda_{1}, v_{1}$:
$$
Tv_{1} = \lambda_{1}v_{1} = v_{1}
$$
where $v_{1} = \pi^{\top}$, the stationary/equilibrium distribution.

Follow the same idea, for any probability distribution $p(t)$ at time $t$ in the eigenbasis of $T$:
$$
p(t) = \sum_{k} c_{k} v_{k}
$$
After n steps, (time = $n\tau$), 
$$
p(t+n\tau) = p(t)T^{n} = \sum_{k} c_{k} \lambda_{k}^{n} v_{k}
$$
- for $k=1$, $\lambda_{1}=1$, the term stays constant
- for $k>1$, $0<\lambda_{k}<1$, as $n$ grows, $\lambda_{k}^{n} \to 0$, the term disappear. This is exactly why starting from the second eigenvector, the eigenvectors all represent *decaying modes*. and the eigenvalues of those eigenvectors tell us *how fast each mode decays*

**Decaying timescale**

Assuming my system has a set of possible states: $1,2,\dots, N$, the probability vector at time $t$:
$$
p_{t} = (p_{1}(t), p_{2}(t), \dots, p_{N}(t))
$$
The evolution of the system is governed by a master equation:
$$
\frac{d}{dt} p(t) = p(t) K
$$
where $K$ defines the rate transitions between states (unit: $s^{-1}$, how many transitions per unit time between state $i$ and state $j$). (*This is different to the $T$ transition matrix, which defines the probability of state $i$ and state $j$ transition after time $\tau$*).

The solution to this linear ordinary differential equation is:
$$
p(t+\tau) = p(t) e^{ \tau K }
$$
$$
e^{ \tau K } := \sum_{n=0}^{\infty} \frac{(\tau K)^{n}}{n!}
$$
since for any form like $\frac{dy}{dt} = ky$, they all have a general solution as $y(t)=y_{0}e^{ kt }$.

Also, since $T(\tau)$ comes from a continuous-time Markov process, by definition, we have
$$
p(t+\tau) = p(t)T(\tau)
$$

Thus:
$$
T(\tau) = e^{ \tau K }
$$
The eigenvalues are related to exponential decays:
$$
\lambda_{k} = e^{ - \tau/t_{k} }
$$
where $-\frac{1}{t_{k}} = u_{i}$, and $u_{i}$ is the eigenvalues of the $K$. $\lambda_{1}=1$ correspond to $t_{1}=0$, meaning *no decay at all* at the stationary/equilibration state

Finally, we get the relationship between the timescales of decay and the eigenvalues of the transition matrix $T$.
$$
t_{k} = - \frac{\tau}{\ln \lambda_{k}}
$$


# 3. Methods to increase sampling conformational space

As a prerequisite of constructing valid MSMs, we need to sample the conformational space to get all the relevant conformational states. Below are some often used methods to do this:
- **simulations with different seedings**: when multiple different structures of the protein are available, initiate simulations for them and combine the trajectories
- **String method calculations**[^1]
- **Adaptive sampling** (the FAST algorithm)[^2]
- Brute force simulation for long long time (like D. E. Shaw Research group)

# 4. Real examples and good practices

check the following github:
```text
https://github.com/moldyn/HP35
```


# Reference
[^1]: Pan, A. C.; Sezer, D.; Roux, B. Finding transition pathways using the string method with swarms of trajectories. J. Phys. Chem. B 2008, 112 (11), 3432−40.
[^2]: Zimmerman, M. I.; Bowman, G. R. FAST Conformational Searches by Balancing Exploration/Exploitation Trade-Offs. J. Chem. Theory Comput. 2015, 11 (12), 5747−57.
