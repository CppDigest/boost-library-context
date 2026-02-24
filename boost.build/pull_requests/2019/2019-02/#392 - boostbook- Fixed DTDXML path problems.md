# #392 boostbook: Fixed DTDXML path problems [Merged]

> Username: Kojoley  
> Created at: 2019-02-16 19:32:06 UTC  
> Updated at: 2021-10-02 21:19:17 UTC  
> Merged at: 2019-02-23 15:25:29 UTC  
> Closed at: 2019-02-23 15:25:29 UTC  
> Url: https://github.com/boostorg/build/pull/392  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-02-16 20:03:21 UTC  
> Updated_at: 2019-02-17 22:50:02 UTC  
> Url: https://github.com/boostorg/build/pull/392#issuecomment-464379308  

I would appreciate if you point me how I could make this:  
  
```  
xml reference : reference.dtdxml : <location>. ;  
explicit reference ;  
  
boostbook boostbook : boostbook.xml : <dependency>reference ;  
```  
without `<location>.` (to not pollute working tree).  
  
I would like to have something like:  
```  
xml reference : reference.dtdxml : : : <xsl:path>path/to/this/target/location ;  
explicit reference ;  
  
boostbook boostbook : boostbook.xml : <dependency>reference ;  
```

---
