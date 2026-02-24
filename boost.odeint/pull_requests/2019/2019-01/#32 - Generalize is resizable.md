# #32 Generalize is resizable [Closed]

> Username: lubkoll  
> Created at: 2019-01-12 22:31:55 UTC  
> Updated at: 2019-01-24 19:03:19 UTC  
> Closed at: 2019-01-24 19:03:19 UTC  
> Url: https://github.com/boostorg/odeint/pull/32  

I am sure I should have found the 'voider' or 'void_t' somewherein boost, but I failed:( Sry, please point me to the correct place.   
Else I hope you like this pull request. I came across the point when trying to use odeint with dynamic Eigen vectors. While I could just specialize the is_resizeable I consider it preferable to auto-detect the resize function and only opt-out if your resize-function does not resize:)  
Please let me know if you follow my reasoning or have another perspective.

---
