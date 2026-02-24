# #490 - Boost versions below 1.75 are not supported [Closed]

> Username: vinniefalco  
> Created at: 2021-01-25 17:12:26 UTC  
> Updated at: 2021-01-28 10:54:40 UTC  
> Closed at: 2021-01-28 10:54:40 UTC  
> Url: https://github.com/boostorg/json/issues/490  

The check for  
```  
#if BOOST_VERSION >= 107300  
```  
in except.ipp should be changed to  
```  
#if ! defined(BOOST_JSON_STANDALONE)  
```  
because Boost.JSON no longer supports versions of Boost below 1.75. The check for 1.73 was added only before the first official Boost release, so that users could test with their OS-provided Boost.
