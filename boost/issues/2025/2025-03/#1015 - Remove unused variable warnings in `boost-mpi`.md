# #1015 - Remove unused variable warnings in `boost-mpi`. [Open]

> Username: MathieuDutSik  
> Created at: 2025-03-02 17:13:33 UTC  
> Updated at: 2025-03-02 17:13:33 UTC  
> Url: https://github.com/boostorg/boost/issues/1015  

When compiling boost code on MacOS (with boost obtained by homebrew), I obtained the following warnings:  
```  
/opt/homebrew/Cellar/boost/1.87.0/include/boost/mpi/detail/request_handlers.hpp:220:39: warning: unused parameter 'comm' [-Wunused-parameter]  
  220 |   serialized_data(communicator const& comm, packed_iarchive& ar) : m_archive(ar) {}  
  
/opt/homebrew/Cellar/boost/1.87.0/include/boost/mpi/communicator.hpp:1424:71: warning: unused parameter 'primitive' [-Wunused-parameter]  
 1424 |                                  std::vector<T,A>& values, mpl::true_ primitive) const  
  
/opt/homebrew/Cellar/boost/1.87.0/include/boost/mpi/communicator.hpp:1548:39: warning: unused parameter 'primitive' [-Wunused-parameter]  
 1548 |                            mpl::true_ primitive) const  
  
/opt/homebrew/Cellar/boost/1.87.0/include/boost/mpi/communicator.hpp:1634:39: warning: unused parameter 'primitive' [-Wunused-parameter]  
 1634 |                            mpl::true_ primitive) const  
  
/opt/homebrew/Cellar/boost/1.87.0/include/boost/mpi/collectives/broadcast.hpp:107:30: warning: unused parameter 'non_mpi_datatype' [-Wunused-parameter]  
  107 |                  mpl::false_ non_mpi_datatype)  
  
/opt/homebrew/Cellar/boost/1.87.0/include/boost/mpi/collectives/reduce.hpp:81:33: warning: unused parameter 'op' [-Wunused-parameter]  
   81 |               T* out_values, Op op, int root, mpl::false_ /*is_mpi_op*/,  
  
/opt/homebrew/Cellar/boost/1.87.0/include/boost/mpi/collectives/reduce.hpp:96:71: warning: unused parameter 'op' [-Wunused-parameter]  
   96 |   reduce_impl(const communicator& comm, const T* in_values, int n, Op op,  
  
/opt/homebrew/Cellar/boost/1.87.0/include/boost/mpi/collectives/scan.hpp:68:16: warning: unused parameter 'op' [-Wunused-parameter]  
   68 |             Op op, mpl::false_ /*is_mpi_op*/, mpl::true_ /*is_mpi_datatype*/)  
```  
  
The solution that I use is to add `[[maybe_unused]]`. Would that be acceptable for boost code? If so, I can create a PR for that.
