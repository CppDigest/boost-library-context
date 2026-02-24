# #109 - Empty description in some cases [Closed]

> Username: vinniefalco  
> Created at: 2021-10-31 16:05:52 UTC  
> Updated at: 2022-01-09 21:58:32 UTC  
> Closed at: 2022-01-09 21:58:32 UTC  
> Url: https://github.com/boostorg/docca/issues/109  

![image](https://user-images.githubusercontent.com/1503976/139592276-8854fe8a-08c2-47bc-9978-d08638c9ba9c.png)  
  
The javadoc:  
```  
/** A CharSet containing the alphanumeric characters  
  
    @par BNF  
    @code  
    ALNUM       = ALPHA / DIGIT  
  
    ALPHA       =  %x41-5A / %x61-7A  
                ; A-Z / a-z  
  
    DIGIT       =  %x30-39  
                ; 0-9  
    @endcode  
  
    @par Specification  
    @li <a href="https://datatracker.ietf.org/doc/html/rfc5234#appendix-B.1"  
        >B.1. Core Rules (rfc5234)</a>  
  
    @see  
        @ref find_if,  
        @ref find_if_not.  
*/  
```
