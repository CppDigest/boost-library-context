# #328 - Namespace in `std::variant` header collidies with `boost::multiprecision` serialization namespace [Closed]

> Username: jeffro256  
> Created at: 2025-04-24 03:46:34 UTC  
> Updated at: 2025-04-25 03:13:57 UTC  
> Closed at: 2025-04-25 03:13:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/328  

If you include both headers `<boost/multiprecision/cpp_int.hpp>` and `<boost/serialization/std_variant.hpp>`, you will get an error message along the lines of `error: redefinition of 'mp'`. This is because in the `<boost/multiprecision/cpp_int/serialize.hpp>` header, there is a *namespace alias definition* `boost::serialization::mp`: https://github.com/boostorg/multiprecision/blob/ce635570f667266864c0f18d1d9accd7b591486a/include/boost/multiprecision/cpp_int/serialize.hpp#L22. This causes a clash with the *named namespace definition* `boost::serialization::mp`: https://github.com/boostorg/serialization/blob/8a8c62864f05732131bf6785d54466d8ac6cd477/include/boost/serialization/std_variant.hpp#L80  
  
The named namespace definition in `std_variant.hpp` is less than half the age of the other and is mainly "detail" logic, so it may be better for backwards compatibility to change `std_variant.hpp`. Although, it is definitely arguable that the namespace alias definition in `cpp_int/serialize.hpp` is "uglier". However, one or both of them has to change.

---

## Comment 1

> Username: mborland  
> Created at: 2025-04-24 19:27:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/328#issuecomment-2828652079  

Fixed on the multiprecision side since we shouldn't be injecting namespace aliases into someone else's namespace.
