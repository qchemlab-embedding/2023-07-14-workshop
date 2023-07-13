---
layout: episode
title: Subsystem methods and the Frozen Density Embedding - a gentle introduction
teaching: 15
exercises: 0
questions:
  - What are the assets of the subsystem methods, what are the constraints?
  - What will we (specifically) focus on in our calculations?
objectives:
  - Introduce the Frozen Density Embedding method.
---

## Subsystem methods and the Frozen Density Embedding (FDE)

Key ideas:

* Subsystem methods:
  * partition a molecular system into subsystems
  * calculate each subsystem separately
  * "combine" the results

* FDE:
  * partitioning is in terms of the electron density of a system,
  * in calculation on each subsystem, the presence of other subsystems is accounted for through the **embedding potential**,
  * the embedding potential depends on the electron densities of all subsystems,
  * the subsystem of interest is **active**, other subsystems are its **environment**,
  * by exchanging the roles of subsystems ("active"/"environment"), one can self-consistently optimize their electron densities
    * this is the **freeze-and-thaw** procedure

<img src="{{ site.baseurl }}/img/fde.png" width="70%">



### Further read

* "Quantum-chemical embedding methods for treating local electronic excitations in complex chemical systems", DOI:10.1039/C2PC90007F (also available in our Zotero) 


