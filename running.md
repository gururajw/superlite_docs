CODE USE INSTRUCTIONS
=====================

SuperLite requires the above specified input files (input.* and data.* files) to run. The input.* files are provided some test models in the *Input* and *Testsuite* folders. The model independent data files are included in the *Data* folder. The following procedure sets up and runs an example SuperLite simulation:

Prepare simulation directory
---

Create a directory where you want to run the SuperLite simulation and change directory.

```
>> mkdir -p ~/sim-superlite/test/run001
>> cd ~/sim-superlite/test/run001
```

Copy the executable superlite file from your SuperLite build directory created in the [installation](/installation.md#install-the-code) step.

```
>> cp ~/superlite/build/superlite .
```

**Note:** If you have created a separate build directory for the serial and the parallel versions of the code, copy the executable from the appropriate build directory.

Create a symbolic link to the atomic data in your SuperLite *src/Data* directory.

```
>> ln -sf ~/superlite/src/Data/* .
```

Setup simulation
---

Two input files are essential for running SuperLite - input.par and input.str. The input.par file contains input parameters and the input.str file contains the input profile. For the details on how to configure these files see the [Simulation Input and Output](/inputpar.md) section.

Copy the input files for a test case provided in the *src/INPUT* directory

```
>> cp ~/superlite/src/Input/input_w7.str input.str
>> cp ~/superlite/src/Input/input.par.lte input.par
```

Run simulation
---
For the serial executable
```
>> ./superlite
```

For the parallel executable
```
>> mpirun -n num_of_cores superlite

(or mpirun -n num_of_cores ./superlite if above doesn't work.)
```

If everything is set up correctly, the code should start running. When you see the message "SuperLite finished." on your screen, the model has successfully run.

**Notes:**
1. To see the terminal output set `in_io_grabstdout` to false in the input.par, and to save it to a file simultaneously use "tee" command, as in example below. By default, `in_io_grabstdout` is set to true, and the output is saved to the out.log file without displaying it to the teminal.

```
> mpirun -n num_of_cores superlite | tee "output.log"
```

2. Use appropriate number of cores for parallel run. For example, if you have a machine with 4 cores with 2 processors each, you may choose to devote 3 cores for running SuperLite. In this case, you would set `in_nomp` to 2 so that two OMP threads per MPI rank are used. It is more efficient to have higher number of OMP threads than higher MPI ranks. For example, a model would run faster with 2 MPI ranks and 4 OMP threads each than 4 MPI ranks and 2 OMP threads each.

  The maximum number of cores depends on the simulation setup. The upper limit is restricted by the number of grid points in the simulation, so that thereis at least one grid cell per core for domain decomposed calculations. For the Monte Carlo (domain replicated) calculations, more the number of cores the faster the code will finish. However, it is recommended to set up the model such that there are at least 10-20 MC particles per cell per group bin per MPI rank to have better statistics and to reduce the MC noise.
