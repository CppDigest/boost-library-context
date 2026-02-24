# #23 - Ordered comparison of pointer with integer zero [Closed]

> Username: BenWiederhake  
> Created at: 2015-09-17 15:05:20 UTC  
> Updated at: 2015-09-17 17:54:00 UTC  
> Closed at: 2015-09-17 17:54:00 UTC  
> Url: https://github.com/boostorg/coroutine/issues/23  

tl;dr: Would it make sense to make a PR with all `0 < ptr` occurrences replaces by `0 != ptr`?  
  
I use gcc and boost for some little project, and want to use `-Wextra` for my code in order to prevent mistakes on my side.  
  
I see the following warning within the boost headers:  
  
```  
$(BOOST_ROOT)/coroutine/asymmetric_coroutine.hpp:1812:23: \  
warning: ordered comparison of pointer with integer zero [-Wextra]  
     BOOST_ASSERT( 0 < sp);  
                       ^  
```  
  
It seems that the whole of Boost.Coroutine is written in this style, so I guess there is some benefit to it. However, since pointers shouldn't be negative (but that's just my naive intuition speaking), the only violation would be `0 == sp`.  
  
If my assumptions is correct, then the proposed `0 != sp` would have these advantages:  
- same semantics  
- more expressiveness (should probably compare against `NULL` or `nullptr` instead of using literal zero, but that's not so important)  
- no/less warnings in gcc  
  
... and no disadvantages.  
So, what do you think?

---

## Comment 1

> Username: olk  
> Created at: 2015-09-17 17:04:10 UTC  
> Url: https://github.com/boostorg/coroutine/issues/23#issuecomment-141151344  

yes, I'll will provide a patch the next days to remove all warnings  
thx

---

## Comment 2

> Username: BenWiederhake  
> Created at: 2015-09-17 17:45:42 UTC  
> Url: https://github.com/boostorg/coroutine/issues/23#issuecomment-141163815  

I went for the "0 != ptr" style. I considered the following alternatives:  
- `nullptr` is specific to c++11 or higher. Bad.  
- `NULL` is nice, but may introduce subtle problems when trying to make sure that it's defined.  
- `0` is really bad, but has the least potential for subtle bugs, and is guaranteed to be as portable as the previous style.
