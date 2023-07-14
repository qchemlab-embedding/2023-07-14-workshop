---
layout: episode
title: FDE calculations in DIRAC (moved to later)
teaching: 0
exercises: 0
objectives:
  - Run FDE calculations in DIRAC.
  - ... we will come back to this lesson in the next workshop
---



## Setup

- Here, our test system is the water dimer. We will calculate the components of the electric polarizability tensor of:

  1. the water dimer, treated as a supermolecular system ("supermolecule"),
  2. each water molecule of that dimer without the presence of the other molecule ("isolated-1" and "isolated-2"),
  3. each water molecule of that dimer with the presence of the other molecule accounted for through the embedding potential ("fde-1" and "fde-2"),
  4. each water molecule of that dimer with the presence of the other molecule accounted for through the embedding potential, and optimized in 2 freeze-and-thaw cycles ("fnt-1" and "fnt-2").
  
  In these calculations, we use:

  - the water dimer geometry taken from a molecular database:


  - the single water molecules are "cut" out from the dimer:



  - we do not reoptimize the geometries of single water molecules for calculations 2.-4.; this means that the results from these calculations already include *some* environmental effects, namely the effects on the geometry (the so-called "mechanical coupling", see REF)

  - in all calculations, we use the numerical grid corresponding to the supermolecule (we calculate and export all real-space data on that grid),

  - in all calculations, we use the Dirac-Coulomb (DC) Hamiltonian, the DFT method with the BP86 exchange-correlation functional, and the cc-pVDZ basis set.

  - molecular input files are available [here](LINK)


### Calculations on supermolecule and isolated subsystems

- These calculations do not need any FDE input; can be done analogously to what we showed in [the first exercise](03-DIRAC-first-steps.md).

- Inputs are available [here](LINK).

- We need to export the numerical grid from the calculations on a supermolecule and reformat this file. Once we retrieved the `numerical_grid` file, we do this with:

  ```shell
  module load python
  /net/pr2/projects/plgrid/plggqcembed/devel/dirac/utils/fde-mag/dft_to_fde_grid_convert.py numerical_grid
  ```

### FDE calculations (point 3.)

1. First, let's assume that `h2o-2.xyz` is the "environment" and `h2o-1.xyz` is the "active" subsystem:

  - we first need to calculate the electron density of the "environment" and export it
    - Inputs and run scripts:
    - The electron density of `h2o-2.xyz` is **exported** ("--get" in run script) on the XXXXXX file; note that we store it in the storage space

  - then, we use the exported electron density to calculate the embedding potential for the "active" subsystem:
    - Inputs and run scripts:
    - The electron density of `h2o-2.xyz` is **imported** ("--put" in run script) from the XXXXXX file
    - The electron density of `h2o-1.xyz`, "updated" by the presence of environment, is optimized and **exported** on the XXXXXX file
    - We also store the wave function data of `h2o-1.xyz` (on the XXX file)

  - finally, we are ready for calculating the molecular property of the `h2o-1.xyz` subsystem
    - Inputs and run scripts:
    - The results are:

2. Now, let's reverse the situation: `h2o-1.xyz` is the "environment" and  `h2o-2.xyz` is the "active" subsystem:

  - we repeat the procedure above, only with the roles of the "environment" and the "active" subsystems exchanged
  - Inputs and run scripts are in XXXX
  - The results are:


### Freeze-and-thaw calculations (point 4.)


- In FDE calculations, we considered the "environment" subsystem to be frozen,
- Now, we will iteratively exchange the roles of the "environment" and the "active" subsystems, until we see the results no longer change


## Results

We can finally collect results. 

- Let us consider the isotropic part of the polarizability tensor, $\alpha_{iso}$ [a.u.]; grep for "Electric dipole polarizability" in the output and find the line with "average" polarizability in atomic units


|                    | supermolecular calc. (1.) | isolated calc. (2.) | fde calc. (3.) | fnt calc. (4.)|
|--------------------|---------------------------|---------------------|----------------|---------------|
|h2o-1               | -                         | x                   | x              | x             |
|h2o-2               | -                         | x                   | x              | x             |
|sum (h2o-1, h2o-2)  | -                         | x                   | x              | x             |
|supermolecule       | x                         | -                   | -              | -             |


Polarizability is an extensive property, i.e., we expect the polarizability values of a supermolecule to be approximately the sum of the polarizability values of composing subsystems.


- Now, look at the values of the tensor's anisotropy and main components - does something strike out? If so, what is the possible explanation?

