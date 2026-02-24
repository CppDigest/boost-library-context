# #155 - Use `boost::core::invoke_swap` with a `const T&` [Open]

> Username: Ukilele  
> Created at: 2023-09-12 07:18:11 UTC  
> Updated at: 2023-09-12 07:54:57 UTC  
> Url: https://github.com/boostorg/core/issues/155  

Hi, this is rather an inquiry than an actual issue.  
`boost::core::invoke_swap` only participates in overload resolution, when the type it gets called with is not const qualified ([invoke_swap.hpp#L81](https://github.com/boostorg/core/blob/develop/include/boost/core/invoke_swap.hpp#L81)). C++23 standardized [std::ranges::zip_view](https://wg21.link/p2321). As part of the proposal, `std::pair` and `std::tuple` got const-qualified overloads of their `swap`-functions (the rationale can be found [here](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2021/p2321r2.html#proxy-reference-changes)). Should this change be somehow reflected in `boost::core::invoke_swap`?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-09-12 07:54:56 UTC  
> Url: https://github.com/boostorg/core/issues/155#issuecomment-1715194241  

If swapping `const` values is now allowed, this would probably mean we need to drop the `is_const` check [here](https://github.com/boostorg/core/blob/5f6fe65eb227375888f7305c73767600ab7f1cf2/include/boost/core/invoke_swap.hpp#L81). But my understanding is that the support for swapping `const`s is more of an exception for a given type (pairs and tuples for now) rather than a rule, so I'm not sure that simply dropping `is_const` is the right way.
