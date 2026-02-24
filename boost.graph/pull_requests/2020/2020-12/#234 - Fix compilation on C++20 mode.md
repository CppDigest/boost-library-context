# #234 Fix compilation on C++20 mode [Merged]

> Username: jcelerier  
> Created at: 2020-12-13 17:21:09 UTC  
> Updated at: 2021-04-21 10:59:25 UTC  
> Merged at: 2021-04-21 10:59:24 UTC  
> Closed at: 2021-04-21 10:59:25 UTC  
> Url: https://github.com/boostorg/graph/pull/234  

MSVC standard library has removed std::not1, thus use the boost alternative.

---

## Comment 1

> Username: jcelerier  
> Created_at: 2020-12-13 17:21:51 UTC  
> Url: https://github.com/boostorg/graph/pull/234#issuecomment-744039608  

@jzmaddock I'd appreciate a review, thanks !

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-12-14 09:16:56 UTC  
> Url: https://github.com/boostorg/graph/pull/234#issuecomment-744300080  

This is fine, but introduces another dependency, I wonder if:  
  
```  
#ifdef __cpp_lib_not_fn  
std::find_if(CG_vec[v].begin(), CG_vec[v].end(),  
                            std::not_fn(detail::subscript(in_a_chain)));  
#else  
std::find_if(CG_vec[v].begin(), CG_vec[v].end(),  
                            std::not1(detail::subscript(in_a_chain)));  
#endif  
```  
  
Is any better?

---

## Comment 3

> Username: jcelerier  
> Created_at: 2020-12-14 10:20:39 UTC  
> Url: https://github.com/boostorg/graph/pull/234#issuecomment-744339542  

Oh right, seems like it's the correct replacement indeed. amending that

---
