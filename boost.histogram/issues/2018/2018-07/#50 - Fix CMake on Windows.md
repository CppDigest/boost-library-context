# #50 - Fix CMake on Windows [Closed]

> Username: HDembinski  
> Created at: 2018-07-02 09:26:55 UTC  
> Updated at: 2018-07-14 17:54:12 UTC  
> Closed at: 2018-07-14 17:54:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/50  

The CMake build does not work on Windows. This is most likely a simple issue related to custom flags that are set.  
  
The script needs to be changed so that special compiler flags are only set for those compilers that understand and need them.
