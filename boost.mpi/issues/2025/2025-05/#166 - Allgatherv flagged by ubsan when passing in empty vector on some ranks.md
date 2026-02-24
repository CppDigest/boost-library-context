# #166 - Allgatherv flagged by ubsan when passing in empty vector on some ranks [Closed]

> Username: shardest  
> Created at: 2025-05-02 19:11:06 UTC  
> Updated at: 2025-05-05 20:20:07 UTC  
> Closed at: 2025-05-05 19:53:49 UTC  
> Url: https://github.com/boostorg/mpi/issues/166  

The following code (expressed with gtest) is flagged by ubsan for undefined behavior:  
  
```  
TEST(Utilities, BoostAllgatherv) {  
    const auto tComm = boost::mpi::communicator{};  
  
    if (tComm.size() != 3) {  
        GTEST_SKIP() << "Must be run with three MPI ranks.";  
    }  
  
    std::vector<int> local_data;  
    if (tComm.rank() == 0) {  
        local_data = {1, 2, 3}; // Rank 0 has data  
    } else if (tComm.rank() == 1) {  
        // Rank 1 has no data  
    } else if (tComm.rank() == 2) {  
        local_data = {4, 5}; // Rank 2 has some data  
    }  
  
    std::vector<int> sizes{3,0,2};  
  
    std::vector<int> gathered_data;  
    boost::mpi::all_gatherv(tComm, local_data, gathered_data, sizes);  
      
}  
```  
  
It seems that the problem is the dereference that occurs on line 41 of all_gatherv.hpp (the dereference of in_values), quoted here:  
  
```  
template<typename T>  
void  
all_gatherv_impl(const communicator& comm, const T* in_values,  
                 T* out_values, int const* sizes, int const* displs, mpl::true_)  
{  
  // Make displacements if not provided  
  scoped_array<int> new_offsets_mem(make_offsets(comm, sizes, displs, -1));  
  if (new_offsets_mem) displs = new_offsets_mem.get();  
  MPI_Datatype type = get_mpi_datatype<T>(*in_values);  
  BOOST_MPI_CHECK_RESULT(MPI_Allgatherv,  
                         (const_cast<T*>(in_values), sizes[comm.rank()], type,  
                          out_values,  
                          const_cast<int*>(sizes),  
                          const_cast<int*>(displs),  
                          type,   
                          comm));  
}  
```  
  
Could in_values just be changed to out_values in that line? It would be silly to call allgatherv when there is nothing to gather on any rank, but it is not silly to call it with some empty vectors as input.

---

## Comment 1

> Username: shardest  
> Created at: 2025-05-02 19:12:57 UTC  
> Url: https://github.com/boostorg/mpi/issues/166#issuecomment-2847916707  

@rawildman

---

## Comment 2

> Username: aminiussi  
> Created at: 2025-05-05 10:57:17 UTC  
> Url: https://github.com/boostorg/mpi/issues/166#issuecomment-2850627649  

Actually, I don't think we need a parameter here, as the template parameter is explicitly given. I need to unrol the full call chain to check that.

---

## Comment 3

> Username: shardest  
> Created at: 2025-05-05 20:20:07 UTC  
> Url: https://github.com/boostorg/mpi/issues/166#issuecomment-2852238554  

Thanks, @aminiussi !
