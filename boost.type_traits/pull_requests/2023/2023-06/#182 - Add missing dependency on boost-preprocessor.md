# #182 Add missing dependency on boost/preprocessor [Closed]

> Username: georgthegreat  
> Created at: 2023-06-26 10:12:05 UTC  
> Updated at: 2023-06-26 15:42:23 UTC  
> Closed at: 2023-06-26 15:32:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/182  



---

## Comment 1

> Username: georgthegreat  
> Created_at: 2023-06-26 10:42:19 UTC  
> Url: https://github.com/boostorg/type_traits/pull/182#issuecomment-1607192074  

Actually this dependency is only needed undef  
```  
#if defined(BOOST_TT_PREPROCESSING_MODE)  
```  
  
So I am not quite sure if it is missing.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-06-26 15:32:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/182#issuecomment-1607729280  

It's not, that's a maintenance only thing.

---
