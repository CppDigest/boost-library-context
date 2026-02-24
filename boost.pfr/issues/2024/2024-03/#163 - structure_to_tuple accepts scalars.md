# #163 - structure_to_tuple accepts scalars [Open]

> Username: anarthal  
> Created at: 2024-03-21 15:55:18 UTC  
> Updated at: 2024-03-21 15:55:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/163  

I don't know if this is intended behavior, since I couldn't find anything in the docs. I was expecting that this:  
  
```  
auto t = pfr::structure_to_tuple(42);  
```  
  
Produced a compile-time error. As long as the argument is a scalar, it's accepted, producing a `std::tuple<int>`, in the above case. From the `static_assert`s in the code, this may be intended, but is pretty surprising behavior.
