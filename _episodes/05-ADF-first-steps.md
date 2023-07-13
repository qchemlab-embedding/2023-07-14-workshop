---
layout: episode
title: Your first calculations in ADF
teaching: 0
exercises: 60
objectives:
  - Run your first ADF calculations.
  - Run your first FDE calculations in ADF.
  - Get familiar with the ADF output.
  - Share tips, links, resources
---

## Running environment and input files

- Input files and run scripts are available [here](https://github.com/qchemlab-embedding/2023-07-14-workshop-exercises/tree/main/adf/geomopt-h2o_zora_b3lyp_dzp)

- In our first calculations, we optimize the geometry of the water molecule, using the ZORA Hamiltonian, the DFT method with the B3LYP functional, and the DZP basis set.

- Let's start with creating a directory for this calculations on your Ares account (e.g., `mkdir workshop_ADF`) and going there (`cd workshop_ADF`)

- To prepare a single calculation in `ADF`, we need two files:

  - a file with molecule specification and `ADF` input, we call this file `h2o_zora_b3lyp_dzp.adf`:


    ```shell
    Task GeometryOptimization
      
    System
        Atoms
           O     0.00000000       0.00000000       0.00000000
           H     0.75757700       0.58613100       0.00000000
           H    -0.75757700       0.58613100       0.00000000
        End
    End
    
    Properties
       NormalModes Yes
    End
    
    Engine ADF
        Symmetry NoSym
        basis
            Type DZP
            CreateOutput Yes
        End
        XC
          HYBRID B3LYP
        End
    
        Relativity
           Formalism ZORA
           Level Scalar
        End
    EndEngine
    ``` 

  - a file with calculation instructions for Ares, we call this file `run.sh`:


      ```shell
      #!/bin/bash -l
      #SBATCH -J adf_test
      #SBATCH -N 1
      #SBATCH --ntasks-per-node=8
      #SBATCH --mem-per-cpu=5GB
      #SBATCH --time=01:00:00 
      #SBATCH -A plgqctda2-cpu
      #SBATCH -p plgrid-testing
      #SBATCH --output="output.out"
      #SBATCH --error="error.err"
      
      scratch=$SCRATCH/adf_tests/simple
      mkdir -p $scratch
      
      cd $SLURM_SUBMIT_DIR
      
      srun /bin/hostname
      
      module purge
      module load adf/2023.101
      
      project=h2o_zora_b3lyp_dzp
      $AMSBIN/ams < $project.adf > $project.out
      ```

- As in `DIRAC` calculations, remember to adjust resources required for the job,

- Submit the calculations with `sbatch run.sh`

## Results

- In `run.sh` script, we redirected the `ADF` output to the `h2o_zora_b3lyp_dzp.out` file, so once the calculation is done, we should see this file in the directory;
  there, grep for "NORMAL TERMINATION" to check whether the calculations finished successfully,

- What else to grep for:

  - "Optimized geometry:" is followed with the new optimized geometry of the H2O molecule:

    ```shell
    Optimized geometry:
     
    --------
    Geometry
    --------
    Formula: H2O
    Atoms
      Index Symbol   x (angstrom)   y (angstrom)   z (angstrom)
          1      O     0.00000000    -0.00572397     0.00000000
          2      H     0.77158369     0.58899298     0.00000000
          3      H    -0.77158369     0.58899298     0.00000000
     
    Total System Charge             0.00000
    ```

  - "Normal Mode Frequencies" - and check whether all frequencies are positive to confirm that the optimized geometry is for the molecule in its ground state.

    ```shell
     -----------------------
     Normal Mode Frequencies
     -----------------------
    
     Index   Frequency (cm-1)   Red. mass (u)  F const (Ha/Bohr^2)   Intensity (km/mol)
         7          1604.8658          1.0837             0.105626              89.5389
         8          3719.7464          1.0442             0.546783              12.8893
         9          3830.7718          1.0821             0.600945             108.7189
    
     Zero-point energy (Hartree):     0.0209
    ```


## Some more advice/good practices:

  - Please redirect large files that you want to keep to a storage space (not to clutter Ares `$HOME` space, [more information](https://docs.cyfronet.pl/display/~plgpawlik/Ares#Ares-Storage))

    - `ADF` by default produces the "ams.results" directory with large data files
    - you can save this directory (in the storage space), but since most `ADF` calculations are fast, it may be cheaper to repeat the calculations rather than to store the data (in case you later discover that you need it),

  - `ADF` has a very liberal approach to thresholds; we will often need tighter thresholds in our calculations (e.g., on optimized energies and properties),

  - As with `ADF` I have less control over the generated data than in `DIRAC`, I pay attention to doing each calculation in a separate directory (avoids accidental overwriting),

  - search in `ADF` manual for more advice and keywords: [link](https://www.scm.com/doc/ADF/)

  - I find it helpful to look at actual `ADF` [examples](https://www.scm.com/doc/ADF/Examples/Examples.html);
    - on Ares, you can also browse this directory: `/net/software/testing/software/ADF/ams2023.101/examples/adf`


