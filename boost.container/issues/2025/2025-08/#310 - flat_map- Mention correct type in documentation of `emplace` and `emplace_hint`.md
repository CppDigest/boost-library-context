# #310 - flat_map: Mention correct type in documentation of `emplace` and `emplace_hint` [Closed]

> Username: Ukilele  
> Created at: 2025-08-15 08:50:29 UTC  
> Updated at: 2025-08-25 21:13:32 UTC  
> Closed at: 2025-08-25 21:13:20 UTC  
> Url: https://github.com/boostorg/container/issues/310  

The documentation of `emplace` says:  
> Effects: Inserts an object x of type T constructed with std::forward<Args>(args)... if and only if there is no element in the container with key equivalent to the key of x.  
  
The documentation of `emplace_hint` says:  
> Effects: Inserts an object of type T constructed with std::forward<Args>(args)... in the container if and only if there is no element in the container with key equivalent to the key of x.  
  
Instead it should mention that it "Inserts an object x of type value_type constructed with [...]".

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-08-25 21:13:32 UTC  
> Url: https://github.com/boostorg/container/issues/310#issuecomment-3221763188  

Thanks for the report!
