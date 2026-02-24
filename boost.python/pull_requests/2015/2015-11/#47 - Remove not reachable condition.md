# #47 Remove not reachable condition. [Merged]

> Username: ya1gaurav  
> Created at: 2015-11-03 08:52:53 UTC  
> Updated at: 2018-04-09 01:18:58 UTC  
> Merged at: 2018-04-09 01:18:58 UTC  
> Closed at: 2018-04-09 01:18:58 UTC  
> Url: https://github.com/boostorg/python/pull/47  

Line no 138-139 suggest if condition satisfy if n_actual <=max_arity :  
        if (n_actual + f->m_nkeyword_values >= min_arity  
            && n_actual <= max_arity)  
So condition at Line no 161 is not reachable.

---

## Comment 1

> Username: hotgloupi  
> Created_at: 2015-12-08 11:13:51 UTC  
> Url: https://github.com/boostorg/python/pull/47#issuecomment-162852695  

LGTM

---
