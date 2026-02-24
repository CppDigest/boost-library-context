# #76 - b2 warning in 1.67.0 (jamfile syntax) [Closed]

> Username: jeking3  
> Created at: 2018-04-28 16:24:18 UTC  
> Updated at: 2018-05-13 15:17:58 UTC  
> Closed at: 2018-05-13 15:17:58 UTC  
> Url: https://github.com/boostorg/predef/issues/76  

This appears to be a warning only:  
  
    boost@1c72cce527e0:/boost$ ./b2 headers  
    /boost/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument   
    $(language)::$(expression)  
    Performing configuration checks  
  
        - default address-model    : 64-bit (cached)  
        - default architecture     : x86 (cached)  
        - symlinks supported       : yes (cached)  
    ...found 1100 targets...  
    boost@1c72cce527e0:/boost$  
  
Line 46 is:  
  
            local key = [ MD5 $(language)::$(expression) ] ;
