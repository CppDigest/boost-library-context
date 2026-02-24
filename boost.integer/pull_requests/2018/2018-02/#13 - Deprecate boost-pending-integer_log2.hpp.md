# #13 [ci skip] Deprecate boost/pending/integer_log2.hpp [Merged]

> Username: NAThompson  
> Created at: 2018-02-11 18:32:48 UTC  
> Updated at: 2018-10-24 17:09:33 UTC  
> Merged at: 2018-10-24 14:59:10 UTC  
> Closed at: 2018-10-24 14:59:10 UTC  
> Url: https://github.com/boostorg/integer/pull/13  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-02-11 19:17:19 UTC  
> Url: https://github.com/boostorg/integer/pull/13#issuecomment-364778989  

A quick check suggests Boost.Random is still using this header?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-02-11 19:24:54 UTC  
> Url: https://github.com/boostorg/integer/pull/13#issuecomment-364779595  

I got 2:  
  
```  
grep -nr 'pending/integer_log2.hpp' .  
./dynamic_bitset/include/boost/pending/lowest_bit.hpp:18:#include "boost/pending/integer_log2.hpp"  
./random/include/boost/random/detail/integer_log2.hpp:19:#include <boost/pending/integer_log2.hpp>  
```  
  
Will put some PRs into those repos in a bit.

---
