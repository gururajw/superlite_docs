<!-- docs/help.md -->

Getting help
============

This section describes how to install prerequisite libraries for the code and some
common troubleshooting steps, if you encountered an error while installing SuperLite.
Please email the author for any additional questions, bug report, and support at
gwagle1@lsu.edu

## Installing prerequisite libraries

Most systems come with either GNU or Intel compilers installed on them.

### To install OpenMP libraries:

Most Linux distributions will also have an openmpi or an openmpi-dev package. If not, then follow the following step.

On Linux OS<br>
`sudo apt install openmpi-bin openmpi-dev openmpi-common openmpi-doc libopenmpi-dev`

On MacOS<br>
`brew install open-mpi`

### To install LAPACK/BLAS libraries:

Most Linux distributions will also have the LAPACK and BLAS packages installed. If not, then follow the following step.

On Linux OS<br>
`sudo apt-get install liblapack-dev libblas-dev`

On MacOS<br>
`brew install lapack openblas`

## Troubleshooting

### List of known issues

1. Intel compilers <br>
If you have Intel Fortran compiler with Math Kernel Library on your system, then you need to enable the following flag in the CMakeList.txt file in the *src* directory to be able to locate the LAPACK and BLAS packages.<br>
`set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -mkl")`

2. MacOS <br>
If you are using latest MacOS and encounter an "argument mismatch" error while compiling that is related to MPI routines, then uncomment the fix on line 75 of the CMakeList.txt file in the *src* directory.
`set(CMAKE_Fortran_FLAGS "${CMAKE_Fortran_FLAGS} -fallow-argument-mismatch")`
