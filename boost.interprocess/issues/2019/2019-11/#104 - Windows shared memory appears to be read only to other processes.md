# #104 - Windows shared memory appears to be read only to other processes [Closed]

> Username: drnuke  
> Created at: 2019-11-20 22:55:40 UTC  
> Updated at: 2019-11-21 00:04:58 UTC  
> Closed at: 2019-11-21 00:04:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/104  

I am seeing execution failures when any process tries to modify data that it didn't create.  
This is with Win10, MSVC-19 (or CLang 9.0), boost 1.70, MS-MPI 2.0  
These tests pass easily under linux.  
  
output...  
PS C:\Users\smith\source\repos\TestInterprocess\out\build\x64-Debug> mpiexec -n 1 .\TestInterprocess  
Hello world from processor <redacted>, rank 0 out of 1 processors  
Rank [ 0 ] Test passes...  
Rank [ 0 ] Test passes...  
PS C:\Users\smith\source\repos\TestInterprocess\out\build\x64-Debug> mpiexec -n 2 .\TestInterprocess  
  
job aborted:  
[ranks] message  
[0] terminated  
[1] process exited without calling finalize  
---- error analysis -----  
[1] on PN1933527  
.\TestInterprocess ended prematurely and may have crashed. exit code 0xc0000005  
---- error analysis -----  
PS  
```  
// TestInterprocess.cpp : Defines the entry point for the application.  
//  
  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <string>  
#include <vector>  
#include <iostream>  
  
#include <mpi.h>  
#include <algorithm>  
  
namespace bi = boost::interprocess;  
  
struct shmem {  
    std::string name;  
    bi::managed_shared_memory segment;  
    shmem(const std::string& n, int id) : name(n + std::to_string(id)), segment(bi::open_or_create, name.c_str(), 65536) { }  
    ~shmem(void) {  
        bi::shared_memory_object::remove(name.c_str());  
    }  
    //Define an STL compatible allocator of that allocates from the managed_shared_memory.  
    //This allocator will allow placing containers in the segment  
    template <typename T>  
    using allocator = boost::interprocess::allocator<T, bi::managed_shared_memory::segment_manager>;  
    template <typename T>  
    auto alloc(void) { return allocator<T>(segment.get_segment_manager()); }  
    template <typename T>  
    using vector = std::vector<T, allocator<T>>;  
};  
  
template <typename T, typename U>  
void CHECK_EQUAL(T&& t, U&& u, int rank) {  
    if (t == u) {  
        std::cout << "Rank [ " << rank << " ] Test passes...\n";  
    }  
    else {  
        std::cout << "Rank [ " << rank << " ] Test FAILURE\n";  
    }  
}  
  
  
int main(int argc, char* argv[])  
{  
    // Initialize the MPI environment  
    MPI_Init(NULL, NULL);  
  
    // Get the number of processes  
    int world_size;  
    MPI_Comm_size(MPI_COMM_WORLD, &world_size);  
  
    // Get the rank of the process  
    int world_rank;  
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);  
  
    // Get the name of the processor  
    char processor_name[MPI_MAX_PROCESSOR_NAME];  
    int name_len;  
    MPI_Get_processor_name(processor_name, &name_len);  
  
    // Print off a hello world message  
    printf("Hello world from processor %s, rank %d out of %d processors\n",  
        processor_name, world_rank, world_size);  
  
    // HostInitializesSiblingSorts  
    using MyVector = shmem::vector<int>;  
  
    const unsigned nElements = 100;  
    MyVector* myvector;  
    //Create a new segment with given name and size  
    shmem s("HostInitsSiblingSorts", world_size);  
    if (world_rank == 0) {  
        //Construct a vector named "MyVector" in shared memory with argument alloc_inst  
        myvector = s.segment.construct<MyVector>("MyVector")(s.alloc<int>());  
        for (int i = 0; i < 100; ++i)  //Insert data in the vector  
            myvector->push_back(i);  
        MPI_Barrier(MPI_COMM_WORLD); // Init done  
        MPI_Barrier(MPI_COMM_WORLD); // Sort done  
  
        if (world_size == 1) {  
            CHECK_EQUAL(100, myvector->size(), world_rank);  
            CHECK_EQUAL(141, myvector->capacity(), world_rank);  
            // Finalize the MPI environment.  
            MPI_Finalize();  
            return 0;  
        }  
    }  
    else { //Sibling process  
        MPI_Barrier(MPI_COMM_WORLD); // Init done  
  
        //Find the vector using the c-string name  
        myvector = s.segment.find<MyVector>("MyVector").first;  
  
        if (world_rank == 1) {  
            std::sort(myvector->rbegin(), myvector->rend());  
            //std::reverse(myvector->begin(), myvector->end());  
        }  
        MPI_Barrier(MPI_COMM_WORLD); // Sort done  
    }  
    CHECK_EQUAL(0, myvector->front(), world_rank);  
    CHECK_EQUAL(99, myvector->back(), world_rank);  
    CHECK_EQUAL(100, myvector->size(), world_rank);  
    CHECK_EQUAL(141, myvector->capacity(), world_rank);  
  
    // Finalize the MPI environment.  
    MPI_Finalize();  
  
    return 0;  
}  
  
```
