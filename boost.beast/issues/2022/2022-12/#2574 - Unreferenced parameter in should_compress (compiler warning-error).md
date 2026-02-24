# #2574 - Unreferenced parameter in should_compress (compiler warning/error) [Closed]

> Username: mojca  
> Created at: 2022-12-04 06:52:27 UTC  
> Updated at: 2022-12-07 06:56:19 UTC  
> Closed at: 2022-12-07 06:56:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2574  

### Version of Beast  
  
boost 1.81.0 beta 1  
  
### Steps necessary to reproduce the problem  
  
Compile a project with Visual Studio 2022 with "treat warnings as errors" flag turned on.  
  
The compiler complains about  
```c++  
    bool should_compress(std::size_t n_bytes) const  
    {  
        return false;  
    }  
```  
```  
C:\PathTo\boost\libs\beast\include\boost/beast/websocket/detail/impl_base.hpp(455,38): error C2220: the following warning is treated as an error  
C:\PathTo\boost\libs\beast\include\boost/beast/websocket/detail/impl_base.hpp(455,38): warning C4100: 'n_bytes': unreferenced formal parameter  
```  
  
I assume that simply changing the code into  
```c++  
    bool should_compress(std::size_t) const  
    {  
        return false;  
    }  
```  
should silence the warning (and error in case "treat warnings as error is enabled").  
  
(It would be awesome if a fix could end up in 1.81.0, if possible.)
