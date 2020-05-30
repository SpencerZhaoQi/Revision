# Fundamentals-2 (Fundamentals-2.pdf)

### [Architecture Types](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/fundamentals-2.pdf#page=14)

-   **Shared memory** system  
    
-   **Distributed memory** system  
    

### [Parallel computing vs. distributed computing](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/fundamentals-2.pdf#page=21)

## Models of Parallel Programming （programmingmodel-2.pdf）

### [Different approaches for programming on a HPC system include:](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/programmingmodel-2.pdf#page=2)

-   **Smart compilers**, which automatically _**parallelise sequential codes**_  
    
-   **Data parallelism**: multiple processors run _**the same operation on different elements of a data structure**_  
    
-   **Task parallelism**: multiple processors run **_different operations_**  
    
-   **Dedicated languages** designed specifically for parallel computers  
    

#### [Compiler Approach](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/programmingmodel-2.pdf#page=4)

-   Automatically **parallelize** the sequential codes  
    
-   Very **conservative**  
    
-   **Re-implementing** the code in a more efficient way  
    
-   Can **remove the dependency** if possible  
    

#### Dependency and Parallelism

Two types of Dependency:  

-   **Control dependency**: waiting for the instruction which controls the execution flow to be completed  
    

-   IF (X!=0) Then Y=1.0/X: Y=1.0/X has the control dependency on X!=0  
    

-   **Data dependency**: dependency because of calculations or memory access  
    

-   **Flow dependency**: A=X+Y; B=A+C;  
    
-   **Anti-dependency**: B=A+C; A=X+Y;
-   **Output dependency**: A=2; X=A+1; A=5;

#### Removing the dependency

**Anti-dependency and output-dependency**  can be removed by  **renaming** the variables(by creating another place)

#### Automatic parallelization

For a for loop, check whether there is **dependency** between different iterations.

#### Features of the Compiler approach

#### 

-   Can work fairly well for some regular problems
-   Fully automatic (efficient) parallelisation is difficult, and **unlikely to be efficient** in general

#### Assisting Compiler

Programmers can assist the compiler by  **writing the code in a way that explicitly expresses**  the parallelism in the program.

-- Using directives， OpenMP

### [Data Parallelism](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/programmingmodel-2.pdf#page=15)

#### Task parallelism vs. Data parallelism

Data Parallelism in OpenMP

-   #pragma omp **parallel for**
-   #pragma omp **parallel** { #pragma omp **for** ...}

Task Parallelism in OpenMP

-   #pragma omp **parallel** { #pragma omp **sections** { #pragma omp **section** ...}}  
    

#### Languages that supports Data parallelism

**F90 and HPF** allow scalar operations to be applied to arrays to support the data parallelism

#### Definition

A data parallel program is  **a sequence of explicitly and/or implicitly parallel statements**

The compiler can  **partition** the data into disjoint sub- domains, one per thread (CPU core)

**Data placement**  is an essential part of data-parallelism (for NUMA machine), and if you get the data locality wrong, you will take a performance hit  

Fortunately, compilers can achieve  **data partition, data allocation/placement, thread communications**  

#### Something about Data parallelism

A data-parallel programming is  **higher-level**  than the message passing-type approach  

-   programmer does **not need** to specify **data partition** and **thread communication** structures  
    

-   this is done by the compiler (inferred from the program and underlying computer architecture)

However

-   **not all algorithms** can be specified in data parallel terms
-   if the program has **irregular communication patterns** then this will be compiled **less efficiently**

#### **[Specially Designed Language](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/programmingmodel-2.pdf#page=23)**

Occam

# OpenMP

# MPI

### [Message Passing Programming](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/mpi-1.pdf#page=2)

### [Message Passing Interface (MPI)](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/mpi-1.pdf#page=3)

### [OpenMP vs MPI](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/mpi-1.pdf#page=4)

-   MPI ==> **distributed-memory** systems  
    
-   OpenMP ==> **shared-memory** systems  
    
-   Both are **explicit** parallelism  
    
-   OpenMP is **higher-level** control (Compiler can automatically parallelise the codes when instructed)  
    
-   MPI is  **lower-level**  control (Data partition, allocation and process communication are conducted by programmers)

### [MPI functions](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/mpi-1.pdf#page=7)

#### Intuitive Interfaces for sending and receiving messages

Send(data, destination), Receive(data_location, source)  

#### [**NOT ENOUGH BECAUSE**](https://warwick.ac.uk/fac/sci/dcs/teaching/material/cs402/mpi-1.pdf#page=8)

#### Express the data in the interface

Early stages:

-   (address, length) for the send interface
-   (address, max_length) for the receive interface
