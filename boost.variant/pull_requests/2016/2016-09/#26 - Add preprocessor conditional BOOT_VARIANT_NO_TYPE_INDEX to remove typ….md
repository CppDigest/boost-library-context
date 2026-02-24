# #26 Add preprocessor conditional BOOT_VARIANT_NO_TYPE_INDEX to remove typ… [Closed]

> Username: felipealmeida  
> Created at: 2016-09-16 02:21:28 UTC  
> Updated at: 2016-10-02 15:37:31 UTC  
> Closed at: 2016-10-02 15:37:31 UTC  
> Url: https://github.com/boostorg/variant/pull/26  

…e_index dependency  
  
This is useful in targets without RTTI and which won't use type_index features. e.g. AVR 8-bit CPUs (Arduinos).

---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-09-16 06:11:13 UTC  
> Url: https://github.com/boostorg/variant/pull/26#issuecomment-247525671  

Boost's TypeIndex works without RTTI http://www.boost.org/doc/libs/1_61_0/doc/html/boost_typeindex/how_it_works.html  
  
There's even a `variant_no_rtti_test` that ensures that `boost::variant` works without RTTI http://www.boost.org/development/tests/develop/developer/variant.html .   
  
This pull request seems invalid, but tell me if I've missed something.

---
