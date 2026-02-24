# #819 - Source files should not include header guards [Closed]

> Username: alandefreitas  
> Created at: 2024-02-09 18:10:52 UTC  
> Updated at: 2024-03-04 18:59:46 UTC  
> Closed at: 2024-03-04 18:59:45 UTC  
> Url: https://github.com/boostorg/url/issues/819  

The .cpp files still have the header include guards ported from the old .ipp files.   
  
```cpp  
#ifndef BOOST_URL_IMPL_PARAMS_REF_IPP  
```  
  
They should be removed.
