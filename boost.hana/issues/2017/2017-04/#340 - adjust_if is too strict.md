# #340 - adjust_if is too strict [Closed]

> Username: viboes  
> Created at: 2017-04-25 18:02:53 UTC  
> Updated at: 2018-01-01 22:47:32 UTC  
> Closed at: 2018-01-01 22:47:31 UTC  
> Url: https://github.com/boostorg/hana/issues/340  

The signature of adjust_if is defined as  
```  
adjust_if:F(T)×(T→Bool)×(T→T)→F(T)  
```  
However the last parameter should be   
```  
adjust_if:F(T)×(T→Bool)×(T→U)→F(U)  
```  
so that transform can be defined in function of adjust_if, isn't it?  
  
The same should apply to adjust.  
  
BTW, the _i appears as a sub-index in the Math signature.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-04-26 07:59:23 UTC  
> Url: https://github.com/boostorg/hana/issues/340#issuecomment-297278779  

> However the last parameter should be  
> 𝚊𝚍𝚓𝚞𝚜𝚝_𝚒𝚏:F(T)×(T→Bool)×(T→U)→F(U)  
  
It can't be, since the predicate will sometimes yield `true` (in which case the type should be `U`), but sometimes yield `false` (in which case the type should be `T`).  
  
> so that transform can be defined in function of adjust_if, isn't it?  
  
Actually, I'm looking at it right now and I think it's a bug in the specification. I don't think `transform` can ever be defined in terms of `adjust_if`, but I have to think it over again (I'm currently jetlagged and not having the clearest thoughts).  
  
> BTW, the _i appears as a sub-index in the Math signature.  
  
Thanks, that's fixed in `develop`.

---

## Comment 2

> Username: viboes  
> Created at: 2017-04-26 16:28:59 UTC  
> Url: https://github.com/boostorg/hana/issues/340#issuecomment-297467016  

Sorry for the format. I did a copy paste.  
  
`adjust_if` could return `F(U)` if `T` is convertible to `U`.  This is in line with replace that compares with a comparable to `T`.

---

## Comment 3

> Username: ldionne  
> Created at: 2018-01-01 22:47:31 UTC  
> Url: https://github.com/boostorg/hana/issues/340#issuecomment-354682460  

You are right: In theory, `adjust_if` with a signature like the following could indeed be implemented:  
  
```  
adjust_if : F(T) × (T→Bool) × (T→U) → F(T)  
```  
  
where `U` is Hana-convertible to `T`. However, this would require additional code to convert the result of the `(T→U)` function to a `T`. I don't have a good reason to think that the generalization is worth the compile-time slowdown and additional complexity, so I won't implement it for the time being.
