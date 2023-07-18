---
layout: episode
title: Your first FDE calculations - ADF, PyADF, DIRAC
teaching: 0
exercises: 60
objectives:
  - Run your first FDE calculations
  - Use PyADF script to run calculations in ADF
---


## Setup

- We start with calculations on the water dimer, in which we treat:

  (1) water dimer as a supermolecular system ("supermolecule"),

  (2) each water molecule of that dimer separately, without the presence of the other molecule ("isolated"), but with the geometry "cut" from the dimer,

  (3) each water molecule of that dimer separately, with the presence of the other molecule accounted for through the embedding potential ("fde"),

  (4) each water molecule of that dimer separately, with the presence of the other molecule accounted for through the embedding potential, and with all subsystems optimized in freeze-and-thaw cycles ("fnt").
  
  In these calculations, we use:

  - geometry of a water dimer from a molecular database (S22 database):

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

  - geometries of single water molecules are "cut" out from the dimer:

      ```shell
      3
      h2o-1 from water dimer - geometry from S22 database; downloaded from http://www.begdb.org/index.php?action=oneDataset&id=4&state=show&order=ASC&by=name_m&method=
      O  -1.551007  -0.114520   0.000000
      H  -1.934259   0.762503   0.000000
      H  -0.599677   0.040712   0.000000
      ```

      ```shell
      3
      h2o-2 from water dimer - geometry from S22 database; downloaded from http://www.begdb.org/index.php?action=oneDataset&id=4&state=show&order=ASC&by=name_m&method=
      O   1.350625   0.111469   0.000000
      H   1.680398  -0.373741  -0.758561
      H   1.680398  -0.373741   0.758561
      ```


  - we do not reoptimize the geometries of single water molecules for calculations 2.-4.; this means that the results from these calculations already include *some* environmental effects, namely the effects on the geometry (the so-called "mechanical coupling", see p.247 of Ref.[^1])



## Running FDE calculations in ADF

- It is possible to run FDE calculations directly in ADF, i.e., to use ADF for all subsystems.
- Please have a look at [these examples in `ADF`](https://www.scm.com/doc/ADF/Examples/Examples.html#fde-frozen-density-embedding).
- If you like, then select one example and see whether you can run the calculation on Ares. You can test various ADF-FDE options and see how do the results compare to the supermolecular results:
  - do they improve the values with respect to "isolated" subsystems?
  - do you see how to run freeze-and-thaw cycles? (hint: look for "RELAX" keywords)?
  - does freeze-and-thaw procedure improve the FDE results (in which environment is kept frozen)?

## Running FDE calculations in DIRAC

- Exercises in progress (Gosia - to do)
- It is possible to run FDE calculations directly in DIRAC, i.e., to use DIRAC for all subsystems.
- Look at [this sample tutorial](https://www.diracprogram.org/doc/release-23/tutorials/fde_nmr/tutorial.html)


## Running FDE calculations in PyADF

- As you see, the preparation of inputs for FDE calculations can be quite cumbersome. This is why we will use the PyADF (see Ref.[^2]) scripting framework on Ares.

- `PyADF` is developed as a Python library; this means that you can use it in your Python script (`import pyadf`)

  - however, it is not open source; the easiest way to access it on Ares is through a module in our storage space (`/net/pr2/projects/plgrid/plggqcembed/groupmodules/pyadf-master.lua`), which then you can use in your run scripts:

    ```shell
    module use /net/pr2/projects/plgrid/plggqcembed/groupmodules
    module load pyadf-master
    ```

- We now demonstrate how to use PyADF to run FDE calculations on the water dimer, as described in [Setup](#setup).

- All input files and run scripts are available [on GitHub](LINK).

- To run `PyADF` on Ares, we need three files:

  - a file with instructions for Ares (`run.sh`)

  - a file that specifies PyADF environment setup for Ares (`jobrunner.conf`)

  - a file in which we specify what to calculate (`water_dimer.pyadf`)

    - this file is actually a Python script (so you can load any Python library, e.g., here we import `numpy` library)

    - we also need instructions for PyADF:

      - PyADF does not have any official manual, but its sources are in `/net/people/plgrid/plggosiao/devel/pyadf`, so please explore and see what is available 

- We submit the calculations with `sbatch run.sh`

- Once the job is finished, we can inspect two files:

  - the `pyadf.*.out` output file:

    - this file contains very basic information (but also we did not ask to print much information in our `*.pyadf` script!)

    - as in our `*.pyadf` script we asked for the information on the dipole moment, we see it printed as:

      ```shell
      Dipole moment for an isolated h2o-1 molecule (in its dimer geometry):  [3.36488359e-01 6.12862631e-01 7.39526242e-12] 0.699160224913389
      Dipole moment for an isolated h2o-2 molecule (in its dimer geometry):  [ 3.92053450e-01 -5.76833811e-01  5.05655201e-11] 0.6974547680885752
      Dipole moment for a supermolecular system (water dimer):  [9.68815690e-01 1.89027790e-02 1.52082564e-11] 0.9690000803524736
      Dipole moment for a h2o-1 molecule with the h2o-2 included as a frozen environment (in their dimer geometries; FDE calculations):  [ 4.22419878e-01  6.23634566e-01 -3.05703171e-12] 0.7532321197113644
      Dipole moment for a h2o-1 molecule with the h2o-2 included as an environment, both subsystems are relaxed in the freeze-and-thaw cycles (in their dimer geometries; FNT calculations):  [ 4.34421085e-01  6.24357592e-01 -8.17731908e-13] 0.7606208528412284
      ```

    - in single point tasks, ADF also calculates the electric dipole moment ($\mu$); we asked to have this data printed in the output, so now we can compare the magnitudes of the electric dipole moment vector for the h2o-1 molecule (and the components of this vector); in particular, we can conclude that:

      - the missing electronic environment effects on the magnitude of the electric dipole moment of the water molecule are substantial (0.699 a.u. for an isolated h2o-1 molecule vs. the reference value of 0.969 a.u. for a supermolecule),

      - the FDE and FNT approaches systematically improve the prediction for the isolated subsystem, i.e., $\mu(isolated) < \mu(FDE) < \mu(FNT) < \mu(supermolecule)$

      - FNT performs slightly better than FDE, thus the relaxation of subsystems' electronic structure recovers some more electronic environmental effects, 

      - the FDE/FNT values are still not that close to the reference supermolecular value,

      - the x-component of the dipole moment vector changes the most upon including the environment (this is expected: the hydrogen bond is aligned with the x-axis); it seems that FDE/FNT well predict the trend, but they poorly describe the changes on the y-component of this vector),


  - the `pyadf.*.out` output file:

    - this is where `PyADF` flushes out the full `ADF` output

- `PyADF` can be used to generate the input file for `ADF` (grep for `ADF Engine Input` in `alloutput.*.out` file)


### Examples of other PyADF scripts for FDE calculations

- For other examples of `PyADF` input files, you can explore the test directory of `PyADF`: on Ares in `/net/pr2/projects/plgrid/plggqcembed/devel/pyadf/test/testinputs`.


## Further read

* [^1] Quantum-chemical embedding methods for treating local electronic excitations in complex chemical systems, DOI: 10.1039/C2PC90007F (also in our Zotero)
* [^2] PyADF - A scripting framework for multiscale quantum chemistry, DOI: 10.1002/jcc.21810 (also in our Zotero)


