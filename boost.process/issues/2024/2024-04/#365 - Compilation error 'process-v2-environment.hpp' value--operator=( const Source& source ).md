# #365 - Compilation error 'process\v2\environment.hpp' value::operator=( const Source& source ) [Closed]

> Username: MrROBUST  
> Created at: 2024-04-22 07:47:51 UTC  
> Updated at: 2024-10-25 00:13:54 UTC  
> Closed at: 2024-10-25 00:13:54 UTC  
> Url: https://github.com/boostorg/process/issues/365  

Is this a typo that 'source.size' missing parens?  
  
https://github.com/boostorg/process/blob/406cd3ecf36c19b94b4376241a4b3b43d1eae308/include/boost/process/v2/environment.hpp#L767  
  
```  
[build] In file included from ...\boost\include\boost/process/v2.hpp:9:  
[build] ...\boost\include\boost/process/v2/environment.hpp(764,35): error: reference to non-static member function must be called; did you mean to call it with no arguments?  
[build]             source.data(), source.size);  
[build]                            ~~~~~~~^~~~  
```
