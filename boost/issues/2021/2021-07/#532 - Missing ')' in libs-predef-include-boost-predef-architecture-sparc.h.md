# #532 - Missing ')' in libs/predef/include/boost/predef/architecture/sparc.h [Open]

> Username: Zawullon  
> Created at: 2021-07-02 10:39:55 UTC  
> Updated at: 2021-07-10 10:49:39 UTC  
> Url: https://github.com/boostorg/boost/issues/532  

Hi,  
There is missing ')' in file libs/predef/include/boost/predef/architecture/sparc.h  
  
```  
...  
#   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv9) || defined(__sparc_v9__)  
...  
#   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv8) || defined(__sparc_v8__)  
...  
```

---

## Comment 1

> Username: thesamesam  
> Created at: 2021-07-10 10:49:39 UTC  
> Url: https://github.com/boostorg/boost/issues/532#issuecomment-877616931  

See https://github.com/boostorg/predef/commit/1be0e4a2d8db15a405f64a6f65507b87c1be7e1a. We had to apply this downstream in Gentoo: https://github.com/gentoo/gentoo/commit/e420d181a07a0dcfef293eb85dc299511daf85cd.
