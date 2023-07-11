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

    - [`ADF` keywords and tutorials](https://www.scm.com/doc/ADF/index.html)


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

  - "Optimized geometry:" is followed with the new optimized geometry of the H2O molecule,
  - "Normal Mode Frequencies" - and check whether all frequencies are positive to confirm that the optimized geometry is for the molecule in its ground state.


## Some more advice/good practices:

  - Please redirect large files that you want to keep to a storage space (not to clutter Ares `$HOME` space, [more information](https://docs.cyfronet.pl/display/~plgpawlik/Ares#Ares-Storage))
    - `ADF` by default produces the "ams.results" directory with large data files, so you may want to redirect this directory to storage, if you think you will need this data later
    - most `ADF` calculations are fast, so it may be cheaper to repeat the calculations rather than to store the data (in case you need it later)

  - `ADF` has a very liberal approach to thresholds; often we will need tighter thresholds in our calculations (e.g., on optimized energies and properties),

  - As with `ADF` I have less control over the generated data than in `DIRAC`, I pay attention to doing each calculation in a separate directory (avoids accidental overwriting),

  - I find it helpful to look at actual `ADF` [examples](https://www.scm.com/doc/ADF/Examples/Examples.html);
    - on Ares, you can copy them from `/net/software/testing/software/ADF/ams2023.101/examples/adf`


