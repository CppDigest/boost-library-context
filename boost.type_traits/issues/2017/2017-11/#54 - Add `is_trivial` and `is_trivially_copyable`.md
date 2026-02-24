# #54 - Add `is_trivial` and `is_trivially_copyable` [Open]

> Username: akrzemi1  
> Created at: 2017-11-03 11:31:02 UTC  
> Updated at: 2017-11-03 11:31:02 UTC  
> Url: https://github.com/boostorg/type_traits/issues/54  

Boost.TypeTraits is missing traits `is_trivial` and `is_trivially_copyable`. It is very tricky to implement them using `has_trivial_constructor` and others. It would be really useful to have `is_trivial` which degrades to `is_pod` on older compilers and is `std::is_trivial` on the newer.
