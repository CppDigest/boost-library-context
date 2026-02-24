# #164 - MPI_CXX_BOOL not defined in Microsoft MPI [Closed]

> Username: yurybura  
> Created at: 2025-04-05 17:28:55 UTC  
> Updated at: 2025-05-05 13:15:03 UTC  
> Closed at: 2025-05-05 13:15:03 UTC  
> Url: https://github.com/boostorg/mpi/issues/164  

When building with the MSVC compiler and Microsoft MPI, the following error occurs:  
```  
boost/mpi/datatype.hpp(217): error C2065: 'MPI_CXX_BOOL': undeclared identifier  
```  
  
`MPI_CXX_BOOL` is not defined in Microsoft MPI interface: https://learn.microsoft.com/en-us/message-passing-interface/mpi-datatype-enumeration  
  
The problem has been introduced in commit 33765a1d301b1e096ad56d9d7f8080ffc178daf3.  
  
Would it be possible to address this by using `MPI_C_BOOL` as an alternative for a boolean datatype? This might resolve the compatibility issue with Microsoft MPI.  
  
@aminiussi Could you please look into this and provide guidance?

---

## Comment 1

> Username: aminiussi  
> Created at: 2025-04-07 09:45:33 UTC  
> Url: https://github.com/boostorg/mpi/issues/164#issuecomment-2782733351  

`MPI_C_BOOL` matches C _Bool, not C++ bool (tables 3.2 and 3.3 of version 3.1 of the MPI standard which goes back to 2015, https://www.mpi-forum.org/docs/mpi-3.1/mpi31-report.pdf#table.3.3). So I'm not comfortable doing that change for everyone.  
Now, it seems that the last version of Microsoft MPI does not support it (that's according to the documentation you provide which is the last available, I need to point out that I do not have access to that implementation).  
If there a way to identify this current version of Microsoft MPI  ? If yes, could you provide a patch/pull request (I won't be able to test on Microsoft implementation) that switch to MPI_C_BOOL for that specific implementation ? (either for Microsoft implementation in general or up to that version, not sure what is best)  
  
I'll test it with implementations I have (2 flavors of Mpich and OpenMPI) and merge it.

---

## Comment 2

> Username: yurybura  
> Created at: 2025-04-07 10:27:24 UTC  
> Url: https://github.com/boostorg/mpi/issues/164#issuecomment-2782847027  

@aminiussi Thanks for the reply.  
  
I don't use Boost.MPI myself, I just try to compile it in the VCPKG package manager (microsoft/vcpkg#44833). After reverting the changes from commit 33765a1 it works on Windows.  
  
As a quick and easy solution I would suggest using something like:  
  
```cpp  
#ifdef MPI_CXX_BOOL  
// use MPI_CXX_BOOL  
#else  
// use the previous implementation for bool based on MPI_BYTE  
#endif  
```  
  
As a long term solution set up CI with the MSVC compiler as done for other Boost libraries.

---

## Comment 3

> Username: aminiussi  
> Created at: 2025-04-07 12:56:58 UTC  
> Url: https://github.com/boostorg/mpi/issues/164#issuecomment-2783249968  

The issue with   
```  
#ifdef MPI_CXX_BOOL  
```  
is that I'm not sure `MPI_<type>` is guaranteed by the MPI standard to be a macro definition.   
And some MPI implementation can assume they're not.  
  
Maybe something similar can be done with MSMPI_VER (https://github.com/microsoft/Microsoft-MPI/blob/master/src/include/mpi.h) but I don't know where/how the value is set. Since there seems to be no `MPI_CXX_<type>` with MS, just testing the definition might be good enough.
