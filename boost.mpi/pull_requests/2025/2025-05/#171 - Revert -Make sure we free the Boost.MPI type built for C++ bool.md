# #171 Revert "Make sure we free the Boost.MPI type built for C++ bool"  [Open]

> Username: aumuell  
> Created at: 2025-05-11 20:30:06 UTC  
> Updated at: 2025-05-11 20:30:06 UTC  
> Url: https://github.com/boostorg/mpi/pull/171  

... "(sice there is no MPI Bool type)"  
  
This reverts commit c5270b066f6f810b2fa184650a4215bb5c83ba27.  
  
Since #163 "Associate primitive bool type with MPI_CXX_BOOL and make it a logical… " was merged, a dedicated data type for MPI Bool is no longer registered. So it should no longer be freed, either. Otherwise, crashes would occur during MPI_Finalize.

---
