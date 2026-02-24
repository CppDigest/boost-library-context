# #538 - Enable build of examples using system installed Boost.Compute [Open]

> Username: ghisvail  
> Created at: 2015-12-02 09:55:38 UTC  
> Updated at: 2017-04-25 15:07:56 UTC  
> Url: https://github.com/boostorg/compute/issues/538  

For c-i / packaging purposes, it would be desirable to allow building the examples (and perhaps the test suite) using a system install of Boost.Compute.  
  
The necessary changes may be as simple as:  
- Make `example` a separate CMake project.  
- Add a call to FindPackage(Boost.Compute) at the beginning of the list file.  
- Replace all occurrences of Boost.Compute by the variables set by FindPackage.  
##
