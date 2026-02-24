# #168 - Move to std::optional in API when available [Open]

> Username: aminiussi  
> Created at: 2025-05-06 09:08:43 UTC  
> Updated at: 2025-05-06 09:08:43 UTC  
> Url: https://github.com/boostorg/mpi/issues/168  

Right now, when we use optional, we take it from boost.  
  
We want to move to std::optional when available (based on **BOOST_NO_CXX17_HDR_OPTIONAL**), unless the user explicitly choose not to by setting **BOOST_MPI_FORCE_BOOST_OPTIONAL** in `boost/mpi/config.hpp`
