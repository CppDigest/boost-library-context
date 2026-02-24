# #59 - Lift restriction on terminal value types not modeling Expression [Open]

> Username: tzlaine  
> Created at: 2018-02-20 03:16:36 UTC  
> Updated at: 2025-06-28 14:40:52 UTC  
> Url: https://github.com/boostorg/yap/issues/59  

- The value of a terminal cannot be another expression.  
  Is this a necessary restriction?  
  
Not really; I probably put that restriction there due to lack of imagination.  Terminals' values should be treated as simple types, but there's no real reason those types can't happen to model Expression.

---

## Comment 1

> Username: 77502jah  
> Created at: 2025-06-28 14:40:52 UTC  
> Url: https://github.com/boostorg/yap/issues/59#issuecomment-3015353257  

/https://github.com/boostorg/yap/issues/59
