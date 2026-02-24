# #224 PGI C++ compiler support: Set DT_SONAME for shared libraries [Merged]

> Username: dkolsen-pgi  
> Created at: 2017-08-02 22:12:45 UTC  
> Updated at: 2021-10-02 22:08:42 UTC  
> Merged at: 2017-08-09 13:57:14 UTC  
> Closed at: 2017-08-09 13:57:14 UTC  
> Url: https://github.com/boostorg/build/pull/224  

When creating a shared library, use the -soname option to set the DT_SONAME field to the simple name of the library.  Setting the DT_SONAME field is necessary so that the shared library can be found when it is referenced by another shared library that is then referenced by an executable.

---
