# #157 - bool is not considered a  is_mpi_logical_datatype [Closed]

> Username: LXYan2333  
> Created at: 2024-03-25 09:48:47 UTC  
> Updated at: 2025-02-26 13:13:32 UTC  
> Closed at: 2025-02-26 13:13:32 UTC  
> Url: https://github.com/boostorg/mpi/issues/157  

### Problem  
  
when I write code to reduce a bool:  
  
```cpp  
bool my_result = false;  
bool reduced_result = boost::mpi::all_reduce(comm, my_result, logical_and<>());  
```  
  
I found that the `std::logical_and<bool>` is not considered a `boost::mpi::is_mpi_op`:  
  
```cpp  
static_assert(boost::mpi::is_mpi_op<std::logical_and<bool>, bool>()); // Static assertion failed  
```  
  
further read the code I found that   
  
```cpp  
static_assert(boost::mpi::is_mpi_datatype<bool>()); // OK  
static_assert(boost::mpi::is_mpi_logical_datatype<bool>()); // Static assertion failed  
```  
  
this caused the boost::mpi to use a `all_reduce_impl` with (tiny) overhead.

---

## Comment 1

> Username: LXYan2333  
> Created at: 2024-03-25 09:55:40 UTC  
> Updated at: 2024-03-25 10:02:52 UTC  
> Url: https://github.com/boostorg/mpi/issues/157#issuecomment-2017618442  

### Expected behaviour  
  
for bool and std::logical_and, the `MPI_C_BOOL` and `MPI_LAND` should be used in reduction, instead of using a `all_reduce_impl` that creats a new `user_op`.
