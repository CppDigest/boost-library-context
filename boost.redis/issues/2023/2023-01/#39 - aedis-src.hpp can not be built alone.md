# #39 - aedis/src.hpp can not be built alone [Closed]

> Username: nejati-deriv  
> Created at: 2023-01-06 14:46:59 UTC  
> Updated at: 2023-01-09 07:34:08 UTC  
> Closed at: 2023-01-09 07:34:08 UTC  
> Url: https://github.com/boostorg/redis/issues/39  

It seems it needs `boost/assert.hpp` included.  
  
```BASH  
third_party/aedis/include/aedis/impl/error.ipp:40:12: error: use of undeclared identifier 'BOOST_ASSERT'  
         default: BOOST_ASSERT(false); return "Aedis error.";  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-01-06 16:39:22 UTC  
> Url: https://github.com/boostorg/redis/issues/39#issuecomment-1373876140  

Thanks. Fixed in https://github.com/mzimbres/aedis/commit/bb8ff903518afd9e73351b6f5781eaf5678d9ad5.
