# #139 Fix build boost_mpi_python on Windows [Merged]

> Username: yurybura  
> Created at: 2022-01-04 14:53:25 UTC  
> Updated at: 2022-06-02 12:10:13 UTC  
> Merged at: 2022-06-02 12:10:13 UTC  
> Closed at: 2022-06-02 12:10:13 UTC  
> Url: https://github.com/boostorg/mpi/pull/139  

On Windows, all code using Python has to link to the Python import library.  
  
`python_for_extensions` is a target defined by Boost.Build to provide the Python include paths, and on Windows, the Python import library, as usage requirements.

---
