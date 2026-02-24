# #1025 - boost::buffer doesn't work on const span/view like polygon input. [Closed]

> Username: icehuli  
> Created at: 2022-06-27 09:06:46 UTC  
> Updated at: 2022-06-29 21:10:57 UTC  
> Closed at: 2022-06-29 21:10:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1025  

I tried to use boost::buffer on `span`-like const type Polygon input. But it doesn't work due to  
https://github.com/boostorg/geometry/blob/6d57aedf7f2b883ed1ac0ffdc59d27c9342d3637/include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp#L527-L528  
  
Since my Polygon only provides a const RingType, `simplify` doesn't work.    
In current version, is it possible to apply boost::buffer on `span`/`view`-like input?

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-06-29 21:10:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1025#issuecomment-1170496691  

Thanks. Fixed by https://github.com/boostorg/geometry/pull/1027
