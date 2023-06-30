# Code Setup Instruction

The following procedures show how to setup superlite executable, either for serial or for MPI-parallel version. The lines that start with '>>' symbol are the terminal commands that you will need to run.

Prerequisites
--
The code requires following libraries:
- The standard GNU or Intel C/C++/Fortran compilers.
- To use the parallel capabilities of the code you need MPI and OpenMP libraries.
- The LAPACK and BLAS libraries.

For further instructions on how to install these libraries for Linux and MacOS, please refer to the [Getting Help](/help.md#installing-prerequisite-libraries) section or contact your system administrator.

Clone the SuperLite Git repository
---
Make a directory where you want to install SuperLite and clone the GitHub repository.

```
>> mkdir ~/superlite
>> cd ~/superlite
>> git clone https://github.com/gururajw/superlite src
```
In this example, superlite/src directory is created in your home directory that includes the modules and the files required to compile the SuperLite code.


Install the code
---

Create a build directory and install the code:

```
>> mkdir build
>> cd build
>> cmake ../src
```

**Note:** This will create a serial build. Add  `-DUSE_MPI=ON -DUSE_OPENMP=ON` flags in the cmake command above to enable MPI+OpenMP for parallel build.

If this step is successful, you should see following message on your screen:

```
SuperLite Version 1.x configured.

-- Configuring done
-- Generating done
-- Build files have been written to: /your/home/directory/superlite/build
```

Compile the code by running make.

```
>> make
```

If everything goes well, you should see the following message on your screen

```
100%] Linking Fortran executable superlite
[100%] Built target superlite
```

You are all set for your adventure!! Follow the steps described in the [Running SuperLite](/running.md) section to run a simulation.

**Note:** One may follow these steps to setup separate build directories for the serial and parallel executables, e.g., build_ser and build_mpi. Each of these build directories will contain an executable file named superlite. Then you will need to copy the right executable to your simulation directory.
