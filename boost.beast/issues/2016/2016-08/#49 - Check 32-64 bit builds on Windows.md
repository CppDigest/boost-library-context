# #49 - Check 32/64 bit builds on Windows [Closed]

> Username: vinniefalco  
> Created at: 2016-08-15 13:48:53 UTC  
> Updated at: 2016-08-26 17:43:09 UTC  
> Closed at: 2016-08-26 17:43:09 UTC  
> Url: https://github.com/boostorg/beast/issues/49  

There are some integer warnings building 64 bit on Windows (bjam and CMake default to 32). The Windows 64-bit CMake build in particular needs to be checked, with  
  
```  
cmake -G"Visual Studio 14 2015 Win64" ..  
```
