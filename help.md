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

3. LAPACK/BLAS installed, but not found
If you encounter an error that the LAPACK/BLAS libraries are not found during the installation, you may have to included the absolute path to your LAPACK and BLAS libraries in the CMakeList.txt file in the *src* directory. Comment out the lines 47 through 53 as below.

```
#find_package (BLAS)
#find_package (LAPACK )
#if(LAPACK_FOUND AND BLAS_FOUND)
#  set(lapckblas_libraries ${BLAS_LIBRARIES} ${LAPACK_LIBRARIES})
#  target_link_libraries(superlite PRIVATE ${lapackblas_libraries})
#endif ()
```

And uncomment lines 55 through 58. At line 55 and 56, update the path in find_library command to the location of libblas.so and liblapack.so files in your Linux machine. (The extension is .dylib instead of .so for MacOS.)
```
find_library (BLA_LIB libblas.so PATH /usr/lib/x86_64-linux-gnu/blas/)
find_library (LAPACK_LIB liblapack.so PATH /usr/lib/x86_64-linux-gnu/lapack/)
target_link_libraries(superlite PRIVATE ${BLA_LIB})
target_link_libraries(superlite PRIVATE ${LAPACK_LIB})
```
