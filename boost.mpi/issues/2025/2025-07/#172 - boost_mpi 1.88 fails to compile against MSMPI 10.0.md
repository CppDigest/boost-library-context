# #172 - boost_mpi 1.88 fails to compile against MSMPI 10.0 [Open]

> Username: pavelliavonau  
> Created at: 2025-07-17 13:03:10 UTC  
> Updated at: 2025-09-18 11:05:57 UTC  
> Url: https://github.com/boostorg/mpi/issues/172  

mpi.h miss MPI_CXX_BOOL type.  
  
"boost\mpi\datatype.hpp: 271 error: 'MPI_CXX_BOOL' undeclared"

---

## Comment 1

> Username: sdebionne  
> Created at: 2025-09-03 07:45:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/172#issuecomment-3248080908  

@aminiussi This has been fixed in develop in https://github.com/boostorg/mpi/commit/58e417214ef842047cd1ec9df602c5fc6241f832, could please merge to master for 1.90?

---

## Comment 2

> Username: aminiussi  
> Created at: 2025-09-18 11:05:57 UTC  
> Url: https://github.com/boostorg/mpi/issues/172#issuecomment-3306847491  

It's merged on master in b5733ba, please note I do not have an Intel MPI available.  
  
Sorry for the delay.
