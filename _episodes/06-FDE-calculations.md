---
layout: episode
title: Your first FDE calculations - ADF, PyADF, DIRAC
teaching: 0
exercises: 60
objectives:
  - Run your first FDE calculations
  - ... we will come back to this lesson in the next workshop
---

## Running FDE calculations in ADF


### Water dimer

- Input files and run scripts are available [here](LINK)

- We start with the calculations on the water dimer, in which we treat:

  (1) the water dimer as a supermolecular system ("supermolecule"),
  (2) each water molecule of that dimer separately, without the presence of the other molecule ("isolated-1" and "isolated-2"), but with the geometry "cut" from the dimer,
  (3) each water molecule of that dimer separately, with the presence of the other molecule accounted for through the embedding potential ("fde-1" and "fde-2"),
  (4) each water molecule of that dimer separately, with the presence of the other molecule accounted for through the embedding potential, and optimized in freeze-and-thaw cycles ("fnt-1" and "fnt-2").
  
  In these calculations, we use:

  - the water dimer geometry taken from a molecular database (S22 database):

    ```shell
    6
    water dimer - geometry from S22 database; downloaded from http://www.begdb.org/index.php?action=oneDataset&id=4&state=show&order=ASC&by=name_m&method=
    O  -1.551007  -0.114520   0.000000
    H  -1.934259   0.762503   0.000000
    H  -0.599677   0.040712   0.000000
    O   1.350625   0.111469   0.000000
    H   1.680398  -0.373741  -0.758561
    H   1.680398  -0.373741   0.758561
    ```

  - the single water molecules are "cut" out from the dimer:



  - we do not reoptimize the geometries of single water molecules for calculations 2.-4.; this means that the results from these calculations already include *some* environmental effects, namely the effects on the geometry (the so-called "mechanical coupling", see REF)



### More advanced systems

- Please go through [these examples in `ADF`](https://www.scm.com/doc/ADF/Examples/Examples.html#fde-frozen-density-embedding), see whether you can run them successfully on Ares

### Tasks/Questions:

  - If you like, then select one example and run the calculation. Test various ADF-FDE options and see how do the results compare to the supermolecular results. Do they improve the values with respect to "isolated" subsystems?
  - Do you see how to run freeze-and-thaw cycles? (hint: look for "RELAX" keywords). Does it improve the FDE results (in which environment is kept frozen)?


## Running FDE calculations in PyADF

WIP

### Further read

* PyADF - A scripting framework for multiscale quantum chemistry, DOI:10.1002/jcc.21810 (also in our Zotero)


