# #34 - interval::hull occludes non-member hull() functions [Open]

> Username: byron-hawkins  
> Created at: 2022-10-06 18:18:52 UTC  
> Updated at: 2022-10-06 18:18:52 UTC  
> Url: https://github.com/boostorg/interval/issues/34  

There are functions `hull(a, b)` and also a static member function `interval::hull(T, T)`. I am using the interval class as a base class. Within my type that is derived from `interval<T>`, I find that it is complicated to invoke the non-member versions of the `hull()` function, because the compiler becomes obsessively glued to the static member function `interval::hull(T, T)`. It effectively occludes the others. Conversely, the static member function serves no purpose, because it performs exactly the same operation as its non-static counterpart. It would be convenient to have this static member function removed.
