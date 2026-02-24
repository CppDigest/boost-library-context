# #81 - Use std::invoke for monadic interface [Open]

> Username: ghost  
> Created at: 2020-01-24 08:18:14 UTC  
> Updated at: 2020-01-24 08:18:14 UTC  
> Url: https://github.com/boostorg/optional/issues/81  

At the moment the functions `map` and `flat_map` use the function call syntax `f(value())`. This allows "only" global/static functions and function objects to be used. But it is not possible to use a member function of the wrapped type which would require different call syntax. Using `std::invoke(f, value())` instead would lead to a more general interface. This was also proposed in [P0798](http://open-std.org/JTC1/SC22/WG21/docs/papers/2019/p0798r3.html#new-section-monadic-operations-optionalmonadic).   
One drawback of this approach would be that (e. g. _non-const_/`const`) overloaded member functions weren't supported since their type couldn't be deferred (I think?). To support all the variations of _no-ref_/`&`/`&&`, `const` and `noexcept`, several new overloads would be needed. And even then, it may not work as expected (?).
