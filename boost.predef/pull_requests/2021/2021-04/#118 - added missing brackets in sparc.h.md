# #118 added missing brackets in sparc.h [Merged]

> Username: tkoecker  
> Created at: 2021-04-28 21:50:17 UTC  
> Updated at: 2021-05-21 14:31:12 UTC  
> Merged at: 2021-05-21 14:31:12 UTC  
> Closed at: 2021-05-21 14:31:12 UTC  
> Url: https://github.com/boostorg/predef/pull/118  

boost 1.76.0 fails to build on sparc as there are brackets missing in sparc.h:  
```  
.../include/boost/predef/architecture/sparc.h:37:38: error: missing ')' in expression  
    37 | #   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv9) || defined(__sparc_v9__)  
       |                                      ^  
.../include/boost/predef/architecture/sparc.h:40:38: error: missing ')' in expression  
    40 | #   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv8) || defined(__sparc_v8__)  
       |                                      ^  
```

---
