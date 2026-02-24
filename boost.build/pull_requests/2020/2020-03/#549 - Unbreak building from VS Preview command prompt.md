# #549 Unbreak building from VS Preview command prompt [Merged]

> Username: MarcelRaad  
> Created at: 2020-03-18 10:55:27 UTC  
> Updated at: 2021-10-02 21:13:27 UTC  
> Merged at: 2020-03-19 03:11:27 UTC  
> Closed at: 2020-03-19 03:11:27 UTC  
> Url: https://github.com/boostorg/build/pull/549  

Commit de026bc61ee changed the order to first detect the unusable vcunk  
toolset before the vc142 and vc141 toolsets. This breaks building from  
the Visual Studio Preview command prompt if no other Visual Studio  
version is detected. Move the vcunk block down to where it was before  
to restore this functionality.

---
