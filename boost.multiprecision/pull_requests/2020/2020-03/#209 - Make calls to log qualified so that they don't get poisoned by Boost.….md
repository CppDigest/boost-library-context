# #209 Make calls to log qualified so that they don't get poisoned by Boost.… [Merged]

> Username: jzmaddock  
> Created at: 2020-03-31 16:20:53 UTC  
> Updated at: 2021-01-30 16:10:06 UTC  
> Merged at: 2020-04-05 12:53:21 UTC  
> Closed at: 2020-04-05 12:53:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/209  

…Log.  
  
Fixes https://github.com/boostorg/multiprecision/issues/207.

---

## Comment 1

> Username: cosurgi  
> Created_at: 2020-03-31 17:20:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/209#issuecomment-606762538  

Try `using boost::multiprecision::log;` instead. It works for me :)

---
