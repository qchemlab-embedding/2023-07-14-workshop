---
layout: episode
title: Your first calculations in DIRAC on Ares
teaching: 0
exercises: 60
objectives:
  - Run your first DIRAC calculations on the Ares cluster.
  - Get familiar with the DIRAC output.
  - Share tips, links, resources
---



## Running environment and input files

- In our first calculations, we compute the energy of the water molecule with the Dirac-Coulomb (DC) Hamiltonian, the Hartree-Fock (HF) method and the cc-pVDZ basis set.

- Let's start with creating a directory for this calculations on your Ares account (e.g., `mkdir workshop_DIRAC`) and going there (`cd workshop_DIRAC`)

- To prepare a single calculation in `DIRAC`, we need three files:

  - a file with molecule specification; here we use the `xyz` format, and call this file `h2o.xyz`:


    ```shell
    3
    our first DIRAC calculations on water molecule (geometry from http://www.sciencedirect.com/science/article/pii/S002228528471277X)
    O     0.00000000       0.00000000       0.00000000
    H     0.75757700       0.58613100       0.00000000
    H    -0.75757700       0.58613100       0.00000000
    ``` 

  - a file with quantum chemistry model specification, we call this file `dc_hf_cc-pvdz.inp`:

    ```shell
    **DIRAC
    .WAVE FUNCTION
    **MOLECULE
    *BASIS
    .DEFAULT
    cc-pVDZ
    **WAVE FUNCTION
    .SCF
    *END OF INPUT
    ``` 

    - [a list of `DIRAC` keywords](https://www.diracprogram.org/doc/release-23/manual/index.html)


  - a file with calculation instructions for Ares, we call this file `run.sh`:

    - on Ares cluster, we can use the installed version of `DIRAC`

      ```shell
      #!/bin/bash -l
      #SBATCH -J dirac_test
      #SBATCH -N 1
      #SBATCH --ntasks-per-node=8
      #SBATCH --mem-per-cpu=5GB
      #SBATCH --time=01:00:00 
      #SBATCH -A plgqctda2-cpu
      #SBATCH -p plgrid-testing
      #SBATCH --output="output.out"
      #SBATCH --error="error.err"
      
      inp=dc_hf_cc-pvdz.inp
      mol=h2o.xyz
      scratch=$SCRATCH/dirac_tests/workshops/workshop_14july2023
      mkdir -p $scratch
      
      cd $SLURM_SUBMIT_DIR
      
      srun /bin/hostname
      
      module purge
      module load dirac/23.0-intel-2021b
      pam-dirac --scratch=$scratch --noarch --mw=900 --aw=1900 --mpi=$SLURM_NPROCS --inp=$inp --mol=$mol
      ```

    - **Note:** if you run `DIRAC` locally (e.g., on your laptop), and have the `DIRAC` code installed, you can simply do (this is a sequential job):

      ```shell
      pam --inp=dc_hf_cc-pvdz.inp --mol=h2o.xyz
      ```

      where `pam` points to the `DIRAC` script available in the program's build directory.


- In the `run.sh` script file, you will always need to adjust resources required for the job. This is done in the beginning of the `run.sh` script file. 

    ```shell
    #!/bin/bash -l
    #SBATCH -J dirac_test                  #<--- job title
    #SBATCH -N 1
    #SBATCH --ntasks-per-node=8            #<--- number of CPUs per 1 node
    #SBATCH --mem-per-cpu=5GB              #<--- memory on each core
    #SBATCH --time=01:00:00                #<--- time (total execution time)
    #SBATCH -A plgqctda2-cpu               #<--- calculation grant name (we will all use this one)
    #SBATCH -p plgrid-testing              #<--- partition name (*)
    #SBATCH --output="output.out"
    #SBATCH --error="error.err"
    ```

  - [(\*) a list of available partitions](https://docs.cyfronet.pl/display/~plgpawlik/Ares#Ares-Jobsubmission)

  - For more information, look at sample scripts on PlGrid [link1](https://docs.cyfronet.pl/display/~plgpawlik/Sample+scripts), [link2](https://kdm.cyfronet.pl/portal/Prometheus:Basics#MPI_jobs), and SLURM commands ([official website](https://slurm.schedmd.com/quickstart.html)).


- We are now ready to run calculations on the Ares cluster:

    - Ares uses SLURM scheduling system, so to submit the calculations, execute:

    ```shell
    sbatch run.sh
    ```

    - to check the status of the calculations, use:

    ```shell
    squeue -u <your user ID>
    ```

    - SLURM has many other useful commands; here is the [official website](https://slurm.schedmd.com/quickstart.html).

  - when the calculation is finished, you should have the following files in your directory (`ls -l`):

    ```shell
    dc_hf_cc-pvdz_h2o.h5
    dc_hf_cc-pvdz_h2o.out
    dc_hf_cc-pvdz.inp
    error.err
    h2o.xyz
    output.out
    run.sh
    ```

    - `DIRAC` produces two output files
      - a text file, `<input file>_<molecule file>.out` (here: `dc_hf_cc-pvdz_h2o.out`), 
      - and the checkpoint file, `<input file>_<molecule file>.h5` (here: `dc_hf_cc-pvdz_h2o.h5`) in `HDF5` format that contains more (and larger) data,

    - the job details and possible error messages are redirected to `error.err` and `output.out` files, which we defined in the `run.sh` script.


- Some more advice/good practices:

  - Ares cluster has a small `$HOME` space, so make sure you redirect large files that you want to keep to a storage space (create your directories there; [more information](https://docs.cyfronet.pl/display/~plgpawlik/Ares#Ares-Storage))
    - text files (e.g. `DIRAC`'s `*.out` file) are typically small; but the data files (`*.h5`) can get large,

    - most of the time, I redirect data files to a directory in the dedicated Ares storage space:

      - first, I define the storage space, e.g. `export data_dir=$PLG_GROUPS_STORAGE/plggqcembed/dirac_tests/workshops_data/workshop_14july2023`
      - then, I tell `pam` script not to produce the `<input file>_<molecule file>.h5` file in my `$HOME`, but to move the data file to storage `--noh5 --get="CHECKPOINT.h5=$bin_dir/CHECKPOINT.h5"` (**note:** the `<input file>_<molecule file>.h5` which we had in `$HOME` is a copy of `CHECKPOINT.h5`)
      - so, in this case, my `run.sh` script is the following:

        ```shell
        #!/bin/bash -l
        #SBATCH -J dirac_test
        #SBATCH -N 1
        #SBATCH --ntasks-per-node=8
        #SBATCH --mem-per-cpu=5GB
        #SBATCH --time=01:00:00 
        #SBATCH -A plgqctda2-cpu
        #SBATCH -p plgrid-testing
        #SBATCH --output="output.out"
        #SBATCH --error="error.err"
        
        inp=dc_hf_cc-pvdz.inp
        mol=h2o.xyz
        scratch=$SCRATCH/dirac_tests/workshops/workshop_14july2023
        mkdir -p $scratch
        
        export data_dir=$PLG_GROUPS_STORAGE/plggqcembed/dirac_tests/workshops_data/workshop_14july2023
        mkdir -p $data_dir
        
        cd $SLURM_SUBMIT_DIR
        
        srun /bin/hostname
        
        module purge
        module load dirac/23.0-intel-2021b
        pam-dirac --scratch=$scratch --noarch --noh5 --get="CHECKPOINT.h5=$data_dir/CHECKPOINT.h5" --mw=900 --aw=1900 --mpi=$SLURM_NPROCS --inp=$inp --mol=$mol
        ```

      - **try yourself:** run this calculation in a new directory, see whether the data file is present in the storage space, and verify that the `.h5` file is not copied to your `$HOME` space.


  - Do not be shy to ask for help on PlGrid in case of cluster troubles/questions (their helpdesk is fantastic, I use it all the time)

  - To do less typing, I like to use aliases for longer `bash` commands. For instance, I use:

    ```shell
    alias qg='squeue -u plggosiao'
    ```

    in my `.bashrc` script, which allows me to type `qg` instead of `squeue -u plggosiao` when I want to check the status of jobs;

  - I like to do each calculation in a separate directory (avoids accidental overwriting; may be easier for postprocessing),

  - Keep track of your calculations, sometimes you will need to restart with different setup/resources (e.g., I am maintaining a "calculation log").



## Reading outputs

- We will primarily look at the `DIRAC` text output file (the default file name is `<input file>_<molecule file>.out`)

- It starts with basic information about the calculations; the most essential is:

  - the program version - this is essential for reproducibility 
    - look for the phrase "Version information" if you compiled `DIRAC` from source
    - when using Ares compilation (of the official `DIRAC` release), then look for "Release" in the output file

  - the repeated input keywords ("Contents of the input file") and molecule specification ("Contents of the molecule file"):
    - this is really handy in case the input files get changed/lost

- Then, there is more information about the molecule (starts from "Detection of molecular symmetry")
  - by default, `DIRAC` tries to detect molecular symmetry (we will often suppress this option)
  - do not worry now about the point group and quaternion symmetries


- We learn more about the quantum chemistry model that was used in the calculations:

  - in particular, note the defaults, approximations, units, and thresholds,
  - information in the `DIRAC` text output that I always check:
    - "Atoms and basis sets" - in particular, did I ask for contracted/uncontracted basis sets in my input file? Does my model need small component functions? How many basis functions do I have? 
    - "Cartesian coordinates" - did I use the correct units in my molecular file? did `DIRAC` correctly recognized the molecule?
    - then, to verify the model:
      - "Hamiltonian defined" - the default is the Dirac-Coulomb Hamiltonian (so we did not need to have the `**HAMILTONIAN` section in the input)
      - "Wave function module" - this is Hartree-Fock calculations (we asked for `.SCF` in the input)

- Since we asked for the energy calculations, `DIRAC` reports energy optimization
  - it starts with "Hartree-Fock calculation", and the summary is in the table under `SCF - CYCLE` together with thresholds applied in optimization 
  - the results we might be interested in are:
    - the "Electronic energy" (here: -85.270254178593248 atomic units) or the "Total energy" (here: -76.081560919738394 atomic units),
    - the energies of HOMO and LUMO orbitals and the gap between them ("HOMO - LUMO gap").


# Tips/comments:

  - The basic `DIRAC` output has the most essential information, but sometimes you may want to learn more details.
    For that, try increasing the print level in your input file; in most cases, you can do this by adding a `PRINT` keyword to a section that interests you, followed with a number larger than 1. 
    For example, try to rerun our example with:

      ```shell
      .PRINT
      5
      ```
      added under `.*SCF`; this will produce a massive output file with details on each SCF iteration step. See `DIRAC` keyword reference for more information.

  - From the `DIRAC` output, we get the numerical data on single properties (e.g., the energy). Other data is stored in separate files; for instance, the wave function (i.e., the optimized molecular orbital coefficients), are stored on the `CHECKPOINT.h5` file in hdf5 format.


  - Where to take the molecular data from?
    - Use online molecular databases. For example, a [NIST database for experimental data](https://cccbdb.nist.gov/exp1x.asp).
    - Look in supplementary information to research publications (and keep the reference). For example, here we did the calculations on the water molecule. Its geometry is taken from the publication (reference in the `*.xyz` file). 
      - If the publication discusses the molecule you want to study, but the authors do not share the data, then don't be shy to write them an email
    - Build the molecule yourself. There are excellent open-source tools that let you build the molecule and preoptimize its geometry (using "poor" methods), and ADF resources available on Ares. For instance:
      - [Avogadro](https://avogadro.cc/)
      - [MolView](https://molview.org/)
      - we can use the `ADF` tools on Ares ([ADF-GUI tool](https://www.scm.com/doc/GUI/Building_molecules.html)),
      - **NOTE:** if you build the molecule yourself in one of these tools, make sure you follow up with the geometry optimization using better methods.
    - If you have the molecular data in formats other then the one accepted by `DIRAC`, you can try the [Open Babel tool](https://openbabel.org/docs/dev/Command-line_tools/babel.html) to convert the files.
    - My advice is to default to `.xyz` file format which has the Cartesian coordinates of all atoms in the molecular system (you don't need to know about the connectivity of atoms, nor molecular symmetry) - this format is understood by most chemical software and the Cartesian coordinates are the easiest to manipulate.
    
  - How to know which DIRAC keywords should be used in the input?
    - All calculations start with the selection of the model (see 01-introduction) - so you know which (1) basis set, (2) method, and (3) Hamiltonian you want to use
      - For the basis sets:
        - you may want to use one of the sets available in `DIRAC` - on Ares, you may want to look in these directories:
          - `/net/pr2/projects/plgrid/plggqcembed/devel/dirac/build-master-public-intel202140/basis`, `/net/pr2/projects/plgrid/plggqcembed/devel/dirac/build-master-public-intel202140/basis_dalton`, `/net/pr2/projects/plgrid/plggqcembed/devel/dirac/build-master-public-intel202140/basis_ecp` 
        - or you may decide to use an external basis set; 
          - e.g., search in [online database](https://www.basissetexchange.org/),
          - in this case, have a look at `DIRAC` tutorials on how to define your own basis set in the input

  - How to know how many cluster resources your calculation require?
    - This will mostly be through trial and error, but:
      - `DIRAC` always runs single-threaded, so you'll always need `#SBATCH -N 1`
      - you can keep the default/advised setup in the preamble (only change the partition and the number of nodes), but adapt the resources with which you want to run `DIRAC`; for that look for the description of various options to the DIRAC's `pam` script, e.g.:

      ```shell
      module load dirac/23.0-intel-2021b
      pam-dirac --help
      ```

      and find "Memory specification:" section

  - If you know you always use certain settings, it might be useful to keep them in `rc` files. For instance, I like to maintain the `~/.bashrc` and `~/.vimrc` scripts (since I use `bash` and `vim`). Also, DIRAC looks for the `~/.diracrc` file, so you can store the settings that you use in all your `DIRAC` calculations.


  - **Do you have other tips from your own experience that you'd like to share?**





