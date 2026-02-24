# #492 C++20: require implicit conversion for hana::is_convertible by static_cast [Open]

> Username: ecatmur  
> Created at: 2021-08-09 10:38:13 UTC  
> Updated at: 2023-08-08 04:28:26 UTC  
> Url: https://github.com/boostorg/hana/pull/492  

In C++20, static_cast can perform aggregate construction (__cpp_aggregate_paren_init). This breaks the example https://github.com/boostorg/hana/blob/master/example/core/convert/is_convertible.cpp#L13 by allowing construction of Employee from its single direct base Person.  
  
Change the maybe_static_cast control expression to require implicit conversion. Since this doesn't allow conversion to cv void, add a to_impl for cv void To.

---

## Comment 1

> Username: ecatmur  
> Created_at: 2021-08-10 07:56:45 UTC  
> Url: https://github.com/boostorg/hana/pull/492#issuecomment-895814315  

Of course, this change would disallow `explicit` converting constructors and conversion operators. An alternative would be to change the example (example/core/convert/is_convertible.cpp).

---

## Comment 2

> Username: gjasny  
> Created_at: 2022-07-06 06:51:40 UTC  
> Url: https://github.com/boostorg/hana/pull/492#issuecomment-1175847493  

Without that patch Visual Studio `v17.3.0-pre.2.0` fails to compile the examples.

---

## Comment 3

> Username: gjasny  
> Created_at: 2022-07-18 11:31:56 UTC  
> Url: https://github.com/boostorg/hana/pull/492#issuecomment-1187149962  

next developer preview `v17.3.0-pre.3.0` fixes compilation.

---
