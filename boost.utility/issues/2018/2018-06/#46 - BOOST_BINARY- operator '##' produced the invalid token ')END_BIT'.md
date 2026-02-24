# #46 - BOOST_BINARY: operator '##' produced the invalid token ')END_BIT' [Open]

> Username: ghost  
> Created at: 2018-06-29 18:23:58 UTC  
> Updated at: 2018-06-29 18:23:58 UTC  
> Url: https://github.com/boostorg/utility/issues/46  

I'm using [vbcc](http://sun.hasenbraten.de/vbcc/), a C cross-compiler, targeting Amiga Kickstart 1.3.  
  
The line  
```C  
custom.dmacon = BOOST_BINARY_U(0000000000000011);  
```  
produces the following error:  
```  
>       custom.dmacon = BOOST_BINARY_U(0000000000000011)  
error 276 in line 96 of "src\main.c": operator '##' produced the invalid token ')END_BIT'  
```
