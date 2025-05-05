# Parallelized-SVD
This repository contains multi-threaded, multi-node, and hybrid (multi-core + multi-node) implementations of SVD and PCA using QR decomposition for tall-and-skinny matrices (TSQR).

The work is inspired by the paper "Direct QR factorizations for tall-and-skinny matrices in MapReduce architectures" by Benson, Gleich, and Demmel, and extends it by introducing task parallelism.

This method is suitable only when the number of columns is much greater than the number of rows (cols >> rows). This condition must hold even for the smallest submatrix processed by a single core or thread. If not, the algorithm may produce incorrect or indeterminate results.

The code uses the Eigen C++ template library for all matrix and vector operations.

The Makefile in each folder assumes that the Eigen library is already on the system path. If not, the files can be compiled manually by specifying the Eigen path like this:

#g++ -I /path/to/eigen serial.cc -o serial

#g++ -I /path/to/eigen -fopenmp omp.cc -o omp

#mpicxx -I /path/to/eigen mpi.cc -o mpi

#mpicxx -I /path/to/eigen -fopenmp hybrid.cc -o hybrid

Command-line arguments
When running the programs, you need to specify the number of rows, number of columns, and (if applicable) number of threads. You also need to provide the path to the input matrix file. The input should be space-separated values (CSV files are not currently supported).

Note on file I/O
Currently, the input matrix is read in a naive way, which may lead to very slow performance on large matrices due to excessive disk reads. If this becomes a bottleneck, a new file reader using buffered I/O will be added.

Example bash script for testing
##!/bin/bash
#make

#echo "omp output"

#./omp 4000000000 4 8

#./omp 2500000000 10 8

#./omp 500000000 50 8

#./omp 150000000 100 8


#echo "mpi output"

#mpirun -np 8 mpi 4000000000 4

#mpirun -np 8 mpi 2500000000 10

#mpirun -np 8 mpi 500000000 50

#mpirun -np 8 mpi 150000000 100



#echo "hybrid output"

#mpirun -np 8 hybrid 4000000000 4 4

#mpirun -np 8 hybrid 2500000000 10 4

#mpirun -np 8 hybrid 500000000 50 4

#mpirun -np 8 hybrid 150000000 100 4

The output (execution time of SVD or PCA) will be written to files in the current working directory. This timing only includes the computation step and does not count the file reading time.

Sanity check for correctness
To validate the results, you should verify the orthogonality of matrices U and V using the following test:

#‖UᵀU - I‖₂

This L2 norm shows how close U is to being an orthogonal matrix. A small value confirms that the output is mathematically correct.

