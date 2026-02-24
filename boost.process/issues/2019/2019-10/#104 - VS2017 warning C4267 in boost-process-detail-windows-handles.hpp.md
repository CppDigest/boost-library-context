# #104 - VS2017 warning C4267 in boost/process/detail/windows/handles.hpp [Closed]

> Username: k15tfu  
> Created at: 2019-10-09 17:56:07 UTC  
> Updated at: 2019-11-22 04:59:18 UTC  
> Closed at: 2019-11-22 04:59:18 UTC  
> Url: https://github.com/boostorg/process/issues/104  

Hi!  
  
After upgrading to boost v1.71 I get the following:  
```  
1>.../boost/process/detail/windows/handles.hpp(42): warning C4267: 'argument': conversion from 'size_t' to 'boost::winapi::ULONG_', possible loss of data  
```
