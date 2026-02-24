# #311 trimmed AppVeyor jobs [Merged]

> Username: joaquintides  
> Created at: 2025-04-14 14:39:40 UTC  
> Updated at: 2025-04-14 16:51:22 UTC  
> Merged at: 2025-04-14 16:51:18 UTC  
> Closed at: 2025-04-14 16:51:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/311  

This addresses #300. The jobs in red have been removed from AppVeyor:  
  
![imagen](https://github.com/user-attachments/assets/eed51740-4af1-4107-be02-e4fad36c4839)  
  
* VS2017 32-bit jobs have been added to Drone as part of this PR.  
* Cygwin32 removed as the environment is no longer relevant.  
* The rest of the removed job were already duplicated in Drone or GHA.  
* GHA has MinGW-w64 jobs but they use a different (newer) version of GCC.  
  
As a result, AppVeyor execution time goes from 3.75 to 1.5-2 hours.

---
