Fundamentals-2 (Fundamentals-2.pdf)
Architecture Types
Shared memory system
Distributed memory system
Parallel computing vs. distributed computing
OpenMP (programmingmodel-1.pdf)
Parallel programming approach
Write serial program and use compiler to automatically parallelize it
OpenMP is used to assist compilers to understand the serial program
Models of Parallel Programming (programmingmodel-2.pdf)
Different approaches for programming on a HPC system include:
Smart compilers, which automatically parallelise sequential codes
Data parallelism: multiple processors run the same operation on different elements of a data structure
Task parallelism: multiple processors run different operations
Dedicated languages designed specifically for parallel computers
    Compiler Approach

Automatically parallelize the sequential codes 
Very conservative 
Re-implementing the code in a more efficient way 
Can remove the dependency if possible
        Dependency and Parallelism

            Two types of Dependency:

Control dependency: waiting for the instruction which controls the execution flow to be completed 
IF (X!=0) Then Y=1.0/X: Y=1.0/X has the control dependency on X!=0
Data dependency: dependency because of calculations or memory access 
Flow dependency: A=X+Y; B=A+C; 
Anti-dependency: B=A+C; A=X+Y; 
Output dependency: A=2; X=A+1; A=5;
        Removing the dependency

            Anti-dependency and output-dependency can be removed by renaming the variables(by creating another place)
        Automatic parallelization

            For a for loop, check whether there is dependency between different iterations.
        Features of the Compiler approach

Can work fairly well for some regular problem
Fully automatic (efficient) parallelisation is difficult, and unlikely to be efficient in general
        Assisting Compiler

            Programmers can assist the compiler by writing the code in a way that explicitly expresses the parallelism in the program.
                -- Using directives， OpenMP
    Data Parallelism

        Task parallelism vs. Data parallelism

            Data Parallelism in OpenMP

#pragma omp parallel for
#pragma omp parallel { #pragma omp for ...}
            Task Parallelism in OpenMP

#pragma omp parallel { #pragma omp sections { #pragma omp section ...}}
        Languages that supports Data parallelism

            F90 and HPF allow scalar operations to be applied to arrays to support the data parallelism.
        Data parallelism

                A data parallel program is a sequence of explicitly and/or implicitly parallel statements.
                The compiler can partition the data into disjoint sub- domains, one per thread (CPU core).  
                Data placement is an essential part of data-parallelism (for NUMA machine), and if you get the data locality wrong, you will take a performance hit. 
                Fortunately, compilers can achieve data partition, data allocation/placement, thread communications.
        Something about Data parallelism

            A data-parallel programming is higher-level than the message passing-type approach
programmer does not need to specify data partition and thread communication structures
this is done by the compiler (inferred from the program and underlying computer architecture) 
            However

not all algorithms can be specified in data parallel terms
if the program has irregular communication patterns then this will be compiled less efficiently

    Specially Designed Language

            Occam
OpenMP
Message Passing Programming I (mpi-1.pdf)
Message Passing Programming
Message Passing Interface (MPI)
OpenMP vs MPI
MPI ==> distributed-memory systems
OpenMP ==>  shared-memory systems
Both are explicit parallelism
OpenMP is higher-level control (Compiler can automatically parallelise the codes when instructed)
MPI is lower-level control (Data partition, allocation and process communication are conducted by programmers)
MPI functions
    Intuitive Interfaces for sending and receiving messages

        Send(data, destination), Receive(data_location, source) 
        >>>NOT ENOUGH BECAUSE<<<

    Express the data in the interface

        Early stages:

            (address, length) for the send interface, (address, max_length) for the receive interface
            shortage:

The data may not occupy contiguous memory locations
Storing format for data may not be the same in a heterogeneous platform
        Triple:

            (address, count, datatype)  for send, (address, max_count, datatype) for receive.
            And now the interfaces are:

send(address, count, datatype, destination, msg_tag)
receive(address, max_count, datatype, source, msg_tag) 
            advantages:

Such a format reflects the fact that a message contains the structures, not just a string of bits
Programmers can construct their own datatype. Handle non-contiguous data
    Communicator (distinguish messages)

        A communicator consists of a group of processes and a communication context 
        Initial communicator: MPI_COMM_WORLD
        Interface: 

            send(address, count, datatype, destination, tag, comm)
            receive(address, maxcount, datatype, source, tag, comm, status), Status holds the information about source, tag and actual size of the received message
    Express source and destination

        They are processes in communicator, and identified by ranks
    Some other issues

        In receiver, tag and source can be a wildcard. ie. MPI_ANY_TAG, MPI_ANY_SOURCE
First six functions
MPI_Send (buf, count, datatype, dest, tag, comm)
MPI_Recv (buf, count, datatype, source, tag, comm, status)
MPI_Init (int *argc, char**argv)
MPI_Comm_size (MPI_Comm comm, int* size)
MPI_Comm_rank (MPI_Comm comm, int* pid)
MPI_Finalize()    
Other functions added in MPI
Convenience (collective operations, topology)
Modularity (communicators, groups)
Efficiency (non-blocking send/receive)
Robustness (ready-mode communication)
Flexibility (datatype)
    Convenience (collective operations,topology)

MPI_Reduce(sendbuf, recvbuf, count, type, op, root, comm), MPI performs op over the data in the sendbuf and put the result in the recvbuf in root.
MPI_Allreduce(sendbuf, recvbuf, count, type, op, comm), MPI performs op over the data in sendbuf and distributes the result back to recvbuf in all processes.
MPI_Barrier(comm), Global synchronization. No processes return from function until all processes have called it.
MPI_Bcast (buf, count, type, root, comm), Broadcast data from root to all processes.
MPI_Gather (sendbuf, sendcount, sendtype, recvbuf, recvcount, recvtype, root, comm), Gather all the data from other processes to root.
MPI_Scatter(sendbuf, sendcount, sendtype, recvbuf, recvcount, recvtype, root, comm), Scatter data from one process to all processes.
    Modularity (communicators, groups)

        MPI supports modular programming via communicators. Provides local namespaces for processes and message tag. All MPI communication operations specify a communicator (process group that is engaged in the communication)
        Creating new communicators - Approach 1

int MPI_Comm_group(MPI_Comm comm, MPI_Group *group) 
int MPI_Group_excl(MPI_Group group, int n, int *ranks, MPI_Group *newgroup) 
int MPI_Group_incl(MPI_Group group, int n, int *ranks, MPI_Group *newgroup) 
int MPI_Comm_create(MPI_Comm comm, MPI_Group group, MPI_Comm *newcomm) 
int MPI_Group_free(MPI_Group *group) int MPI_Comm_free(MPI_Comm *comm)
        Creating new communicators - Approach 2

MPI_Comm_split(comm, colour, myid, *newcomm);
    Robustness (non-blocking send/receive)

        Blocking send

            The sender doesn’t return until the application buffer can be re-used (which often means that the data have been copied from application buffer to system buffer) //note: it doesn’t mean that the data will be received
        Blocking receive

            The receiver doesn’t return until the data have been ready to use by the receiver (which often means that the data have been copied from system buffer to application buffer)
        Non-blocking send/receive

The calling process returns immediately
Just request the MPI library to perform the communication: no guarantee when this will happen
Unsafe to modify the application buffer until you can make sure the requested operation has been performed (MPI provides routines to test this)
Can be used to overlap computation with communication and have possible performance gains
        Testing non-blocking communications

    Efficiency (communication mode)

        The communication modes refers to the send routines:
Standard send: MPI_Send (blocking), MPI_Isend (nonblocking) 
Synchronous send: MPI_Ssend (blocking), MPI_Issend (non- blocking) 
Buffered send: MPI_Bsend (blocking), MPI_Ibsend (nonblocking) 
Ready send: MPI_Rsend (blocking), MPI_Irsend (nonblocking)
        Two Receive routines:
Blocking receive routine: MPI_Recv() 
Non-blocking receive routine: MPI_Irecv() 
    Virtual topology --Cartesian Topology

        MPI_Cart_create(MPI_Comm comm_old, int ndims, int *dims, int *periods, int reorder, MPI_Comm *comm_cart)
        MPI_Cart_rank(comm, coords, rank) 
        MPI_Cart_coords(comm, rank, ndims, coords)
    Flexibility (datatype)

        Derived datatype

            Derived datatype allows the users to construct (derive) their own data types.
            Derived datatypes can be used in:
Grouping non-contiguous data.
Grouping data of different datatypes.
            Provides the opportunity to send this kind of data conveniently and potentially improving performance
        Memory Layout of a Datatype

            Format: {(type_0, offset_0), (type_1, offset_1), ..., (type_n, offset_n)} 
            Examples for primitive datatypes: MPI_Char: {(MPI_Char, 0)}, MPI_Int: {(MPI_Int, 0)}
        Three Attributes to characterize non-contiguous data

            
            MPI_Type_Vector(count, blocklen, stride, oldtype, newtype)
            MPI_Type_create_indexed_block( int count, int blocklength, int array_of_offsets[], MPI_Datatype oldtype, MPI_Datatype *newtype)
            MPI_Type_Indexed(count, lengths[], offsets[], oldtype, newtype)
            MPI_Type_struct(int count, int *array_of_blocklengths, MPI_int *array_of_displacements, MPI_Datatype *array_of_types, MPI_Datatype *newtype)
            MPI_Type_contiguous(int count, MPI_Datatype old_type, MPI_Datatype *new_type_p)
        How are the data with derived data type are sent?

Pack the data elements specified by the memory layout into the contiguous space in system buffer
MPI library sends the data in system buffer
        Why using derived data type can improve performance?  --Two possible solutions to non-contiguous data

Call a MPI_Send for each data block that occupies contiguous space. Calling MPI_Send multiple times, which occurs higher overhead (e.g., handshaking between sender and receiver, metadata enclosed for each message)
Copy the non-contiguous data to a contiguous buffer space and then call a MPI_Send to send the data in the buffer. Needs to copy the data twice: 1) copy the non-contiguous data to a contiguous buffer space; 2) copy the data in the application buffer to system buffer

