# #352 - Jamfile produces an error now [Closed]

> Username: vinniefalco  
> Created at: 2022-08-06 01:27:37 UTC  
> Updated at: 2022-08-08 23:26:12 UTC  
> Closed at: 2022-08-08 23:26:12 UTC  
> Url: https://github.com/boostorg/url/issues/352  

I believe this was introduced with the filesystem warning change, when building our unit tests:  
```  
error: No best alternative for /C:/Users/vinnie/src/boost/libs/url/test/unit/error  
    next alternative: required properties: (empty)  
        matched  
    next alternative: required properties: (empty)  
        matched  
```  
  
It has to be fixed because it is an error
