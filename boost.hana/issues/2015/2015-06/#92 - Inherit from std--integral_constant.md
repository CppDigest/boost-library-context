# #92 - [IntegralConstant] Inherit from std::integral_constant [Closed]

> Username: ldionne  
> Created at: 2015-06-12 21:20:39 UTC  
> Updated at: 2015-09-06 18:32:10 UTC  
> Closed at: 2015-09-06 18:32:10 UTC  
> Url: https://github.com/boostorg/hana/issues/92  

Now that we embrace the usage of `<type_traits>` & related headers, it really makes sense to inherit from `std::integral_constant` in Hana's `integral_constant`. Then, we can return Hana's `integral_constant` from the `<type_traits>` adapted metafunctions to benefit from Hana's arithmetic operators. Also, it might make sense to get rid of the `<boost/hana/ext/std/type_traits.hpp>` header, and to put its content in `<boost/hana/type.hpp>`. Same goes for `<boost/hana/ext/std/utility.hpp>`.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-06 17:38:28 UTC  
> Url: https://github.com/boostorg/hana/issues/92#issuecomment-138103503  

For now, let's not get rid of `ext/std/type_traits.hpp` as suggested above.
