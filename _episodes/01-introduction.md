---
layout: episode
title: Quantum mechanical description of molecular systems - a gentle introduction
teaching: 15
exercises: 0
questions:
  - How do we model molecular systems with ab-initio methods?
  - What are the assets of ab-inition QM modeling, what are the constraints?
  - How does that relate to the project we work on?
  - What will we (specifically) focus on in our calculations?
objectives:
  - Discuss various approximations in quantum chemistry modeling and their implications.
  - Introduce everyone to the project.
  - Indicate key quantum chemistry concepts that are relevant to the project.
---

## About the project

The aim of this workshop is to set up a toolbox that will allow to model large and complex molecules, which contain heavy atoms, in "realistic" environments.
The computer simulations of such ensembles are not easy, since they require the model that includes the **relativistic effects** (scalar and spin-orbit),
have a good description of **electron correlation** and, finally, should account for the presence of an environment (**environmental effects**).
More advanced quantum chemistry methods which are able to encompass the relativistic and electron correlation effects are computationally very expensive,
therefore cannot be used to a molecule and its environment treated as a whole system.

A promising alternative is to use embedding methods, in which the whole system is divided into subsystems:
an *active* molecule of interest and its *environment*,
treated separately by the best (and tailored for them) quantum chemistry models.
A promising example of such method is the **frozen density embedding (FDE)**, on which we focus in this workshop.

We will discuss the modeling of the **electronic structure** of molecules and of their **molecular response properties** - primarily the ones that can be related to
observables in various molecular spectroscopies.


## The ab-initio quantum chemistry modeling - a gentle introduction

### Physical reality vs theoretical models

**physics:**  
- molecules as sets of particles (nuclei + electrons), which are in constant motion and interact (with other particles and external fields)

**modeling:** 
- molecular modeling as a many-body problem -> need for approximations
- approximations determine which physical effects are captured in a model, and how well they are described (examples below)

### Theoretical setup

The purpose of quantum chemistry modeling is to describe how each particle behaves.
If the particle in question is the electron, then one needs quantum mechanical formulations, as they capture electron's dual nature (particle-wave duality).  
Nuclei are much heavier and much slower than electrons, and therefore can be treated as classical particles.
This is why in our studies we will primarily focus on electrons - on predicting **the electronic structure** of molecular systems.

But in the quantum realm, one can only calculate the probability of finding the particle (the electron) at certain time in a given position.
To achieve that, we first introduce a wave function, $\psi(\vec{r}, t)$, for a particle:
- the square of $\psi(\vec{r}, t)$ corresponds to the probability of finding this particle at time $t$ and point in space $\vec{r}$: $P(\vec{r}, t) = \psi^2(\vec{r}, t)$,
- this wave function can be obtained by solving the Schrodinger or Dirac equations, which (both) have the following form:
  $$\hat{H}\psi(\vec{r}, t) = i\frac{d\psi(\vec{r}, t)}{dt}$$, or, in time-independent case, $$\hat{H}\psi(\vec{r}, t) = E\psi(\vec{r}, t)$$,
- the physics of a system is contained in the **Hamiltonian**, $\hat{H}$; it is a sum of kinetic ($\hat{T}$) and potential ($\hat{V}$) energy operators, $\hat{H} = \hat{T} + \hat{V}$,
- the Schrodinger equation describes the "nonrelativistic" particle, while the Dirac equation describes the "relativistic" particle
  1. one difference between the two equations stems from the Lorentz factor
    $$\gamma = \frac{1}{\sqrt{1 - v^2/c^2}}$$
    - if the particle moves much slower than the speed of light, i.e., $v \ll c$, then $v^2/c^2 \approx 0$ and $\gamma \approx 1$ - the "nonrelativistic" approximation
    - if the particle moves with speed close to the speed of light, then $\gamma > 1$ - requires a "relativistic" description
    - $\gamma$ affects the particle's mass, $m = \gamma m_0$ (with $m_0$ - the rest mass of a particle)
    - why it matters? e.g., the average speed of 1s electron equals the nuclear charge (in atomic units), $v_{1s} = Z [a.u.]$, hence the heavier the nucleus, the larger the electron's speed and mass,
  2. another difference between these two equations stems from the coupling of spin and space degrees of freedom (more on that in the coming workshops),
  - the **relativistic effects** on energies, properties, etc., are evaluated as differences of results (energies, properties, etc.) obtained with the relativistic and nonrelativistic description; we can further distinguish:
    - **scalar** relativistic effects (point 1. above)
    - **spin-orbit** relativistic effects (point 2. above)
- the Hamiltonian also describes how particles interact with each other: if we include the electron-electron interaction, then the simplest is to approximate it with the Coulomb potential (as for point charges),
- in our calculations we will primarily use relativistic Hamiltonians, for instance the **Dirac-Coulomb (DC)** Hamiltonian, which uses the Dirac operator for the kinetic energy and the Coulomb operator for the potential energy of the electron-electron interaction,
- we then choose the approximation for the wave function:
  - electrons are fermions - two electrons with the same spin cannot be in the same position, and the wave function must be antisymmetric in electrons' spatial coordinates,
  - the simplest wave function is represented by a determinant of a matrix constructed with functions of one electron ("molecular orbitals") - this is a "Slater determinant",
  - a choice to use one such determinant as a wave function is made e.g., in the **Hartree-Fock (HF)** method,
  - the choice of a wave function determines the description of how the movement of one electron affects the movement of another electron (**the electron correlation**),
- finally, we need to decide how to represent the one-electron functions (molecular orbitals); the typical examples in molecular calculations involve expanding these functions in Gaussian basis sets (as used in `DIRAC`) or in exponential sets (as used in `ADF`),
- finally, we are ready to solve the Schrodinger/Dirac equations - this is done in iterative procedure, meaning that the wave function (i.e., the molecular orbitals) are optimized self-consistently under the conditions imposed by all approximations we introduced on the way.

Thus far, we discussed the basics of the **wave function theory (WFT)** models. These models are expensive - e.g., in our calculations on H2O molecule, we will need to treat 10 electrons, each described with 3 Cartesian coordinates. This makes the wave function dependent on $3\*10 = 30$ spatial coordinates. To simplify this, we can instead consider describing the system through its electron density, $\rho(\vec{r})$, which depends on the 3 Cartesian coordinates only (for any system, irrespective of its size). This choice stands as a basis for methods of the **density functional theory (DFT)**.
Still, the typical usage of DFT methods requires (i) the choice of basis set expansion, (ii) the choice of the Hamiltonian, and (iii) the choice of the **exchange-correlation functional**, which affect the computational cost.


### Quantum chemistry models

To sum up the discussion thus far, in devising the computational model for quantum mechanical calculations, we should be aware of:

- the approximations introduced; "typical" examples:
  - fix nuclei in space = describe electrons only (Born-Oppenheimer approximation),
  - describe each electron by a function in an effective potential of other electrons ("mean-field approximation"),
  - express the one-electron function (molecular orbital) in a set of known functions ("basis set expansion", or "linear combination of atomic orbitals (LCAO)"),

- the components of the quantum chemistry model:
  - how is one electron described:
    - "the basis set"
    - examples: **Gaussian sets (cc-pVDZ, dyall.cv2z, ANO-RCC, etc.)**
  - how are many electrons described
    - "the method"
    - examples: **Hartree-Fock, Coupled-Cluster methods, DFT, etc.**
  - how is physics described (particle motion and interactions):
    - the Hamiltonian
    - examples: **Dirac-Coulomb Hamiltonian, ZORA Hamiltonian, nonrelativistic Hamiltonian, etc.**

- the better description of each = the more accurate model = the higher cost of calculations
  - the Jacob's ladder of chemical accuracy:

     **WFT** methods | **DFT** methods
   <img src="{{ site.baseurl }}/img/jaccobs_ladder_wft.jpg" width="90%"> | <img src="{{ site.baseurl }}/img/jaccobs_ladder_dft.jpg" width="90%">
     
  - the **computational cost** of WFT/DFT methods is $xN^y$, where:
    - $y$ depends on the method, e.g., the formal scaling of the HF method is $y=4$,
    - $N$ is a "system size" expressed in the total number of basis functions,
    - $x$ depends on the Hamiltonian, e.g., the DC Hamiltonian is "4-component" (roughly, $x=4$), while the nonrelativistic Hamiltonians are typically "1-component" (roughly, $x=1$),
    - for instance, for the calculations on the H2O molecule, performed with the DC/HF/cc-pVDZ model[^1], and with no cost reduction (e.g., due to symmetries), we can evaluate the cost as: $4\*131^4$ (see "DIRAC-first-steps" lesson)),


### Further read

* Introduction to computational chemistry, Frank Jensen, Wiley&Sons 2007 (book, also available in our Zotero)

### Footnotes

[^1] we use the notation "Hamiltonian/method/basis set" in these notes


