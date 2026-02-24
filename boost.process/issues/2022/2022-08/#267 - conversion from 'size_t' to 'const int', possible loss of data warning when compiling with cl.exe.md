# #267 - conversion from 'size_t' to 'const int', possible loss of data warning when compiling with cl.exe [Closed]

> Username: k15tfu  
> Created at: 2022-08-25 19:06:56 UTC  
> Updated at: 2022-09-02 12:28:52 UTC  
> Closed at: 2022-09-02 12:28:52 UTC  
> Url: https://github.com/boostorg/process/issues/267  

Hi!  
  
In file boost/process/environment.hpp:  
```  
boost/process/environment.hpp(266,1): warning C4267: 'initializing': conversion from 'size_t' to 'const int', possible loss of data  
```
