# #1 - Redundancy in LEAF_AUTO [Closed]

> Username: uecasm  
> Created at: 2018-11-15 03:40:55 UTC  
> Updated at: 2018-11-15 23:59:58 UTC  
> Closed at: 2018-11-15 23:59:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/1  

https://github.com/zajo/leaf/blob/6c113c51b4e49ef27b1bc9a8d57174ecf7304bab/include/boost/leaf/result.hpp#L14  
  
Why does this use token pasting on `v`, when it will always produce a literal `_r_v` and no other name?

---

## Comment 1

> Username: zajo  
> Created at: 2018-11-15 23:59:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/1#issuecomment-439234228  

Yeah, it's redundant, copy/paste. Thanks for reporting, it is now fixed.
