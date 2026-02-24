# #104 - implement safe version of operator? [Open]

> Username: robertramey  
> Created at: 2021-03-29 20:45:05 UTC  
> Updated at: 2021-05-23 00:06:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/104  

return x1 < x2 ? x1 : x2 generates compile error because operator? required that both t1 and t2 need to be convertible to the same type.  But only safe version of operator? would do this.  Solution implement safe version of operator?

---

## Comment 1

> Username: robertramey  
> Created at: 2021-05-23 00:06:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/104#issuecomment-846479059  

Hmmm have to think about that. x2 and x1 definitely have to be convertible to the same type.  I guess you're right, I need to define/implement operator?(...)
