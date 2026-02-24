# #26 Fix boost::once_flag initialization [Merged]

> Username: Lastique  
> Created at: 2016-03-24 07:33:14 UTC  
> Updated at: 2016-03-24 07:37:19 UTC  
> Merged at: 2016-03-24 07:34:56 UTC  
> Closed at: 2016-03-24 07:34:56 UTC  
> Url: https://github.com/boostorg/context/pull/26  

The initial value is required to be BOOST_ONCE_INIT. Luckily, it is currently equivalent to zero, that's why it worked before.

---

## Comment 1

> Username: olk  
> Created_at: 2016-03-24 07:34:59 UTC  
> Url: https://github.com/boostorg/context/pull/26#issuecomment-200704821  

thx

---
