# #125 - brackets in code blocks confuses things [Closed]

> Username: vinniefalco  
> Created at: 2022-08-05 01:35:25 UTC  
> Updated at: 2022-08-05 01:50:04 UTC  
> Closed at: 2022-08-05 01:50:04 UTC  
> Url: https://github.com/boostorg/docca/issues/125  

For example:  
```  
/** Bracket example  
  
    @par BNF  
    @code  
    host        = IP-literal / IPv4address / reg-name  
  
    IP-literal  = "[" ( IPv6address / IPvFuture  ) "]"  
  
    IPvFuture   = "v" 1*HEXDIG "." 1*( unreserved / sub-delims / ":" )  
/*  
void f();  
```  
  
Causing this stuff:  
```  
C:\Users\vinnie\src\boost\bin.v2\libs\url\doc\reference.qbk:29564: warning: in column:5, unexpected character: @  
C  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-05 01:50:04 UTC  
> Url: https://github.com/boostorg/docca/issues/125#issuecomment-1205950983  

Sorry I put `@endif` instead of `@endcode`... oops.
