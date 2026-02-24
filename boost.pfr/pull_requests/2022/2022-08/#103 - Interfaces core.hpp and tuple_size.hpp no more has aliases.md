# #103 Interfaces core.hpp and tuple_size.hpp no more has aliases [Closed]

> Username: denzor200  
> Created at: 2022-08-27 12:28:51 UTC  
> Updated at: 2022-12-16 16:27:38 UTC  
> Closed at: 2022-12-16 16:27:37 UTC  
> Url: https://github.com/boostorg/pfr/pull/103  

In C++ we can't make specialization for type alias, but for structure can.  
This PR will make https://github.com/boostorg/pfr/issues/100 possible to implement.

---

## Comment 1

> Username: coveralls  
> Created_at: 2022-08-27 12:42:43 UTC  
> Updated_at: 2022-09-01 14:09:20 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#issuecomment-1229185793  

## Pull Request Test Coverage Report for [Build 2972180814](https://coveralls.io/builds/52135321)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/52135321/badge)](https://coveralls.io/builds/52135321) |  
| :-- | --: |  
| Change from base [Build 1768569863](https://coveralls.io/builds/46064091): |  0.0% |  
| Covered Lines: | 337 |  
| Relevant Lines: | 337 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 2

> Username: apolukhin  
> Created_at: 2022-08-31 18:20:17 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#issuecomment-1233267765  

What is the purpose of specializing those two structures?

---

## Comment 3

> Username: denzor200  
> Created_at: 2022-09-01 07:45:28 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#issuecomment-1233880731  

> What is the purpose of specializing those two structures?  
  
Those two structures are not intended to specialize by library's user(i wrote a note in the docs about it, just now). Ability to specialize will be used by library developers.

---

## Comment 4

> Username: denzor200  
> Created_at: 2022-09-01 07:56:03 UTC  
> Updated_at: 2022-09-01 07:57:22 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#issuecomment-1233891624  

> What is the purpose of specializing those two structures?  
  
It may reduce a lot of code, when i continue integrating pfr into fusion using "VIEW manipulator", like this:  
```  
struct A  
struct B;  
struct C;  
  
BOOST_PFR_REGISTRATE_REFLECTABLE(B)  
  
A a{};  
B b{};  
C c{};  
// boost::fusion::for_each(a, [](auto&&){});                     // won't compile  
boost::fusion::for_each(b, [](auto&&){});                        // will use default specialization inside  
boost::fusion::for_each(boost::pfr::view(c), [](auto&&){});      // will use `boost::pfr::tuple_size<boost::pfr::view_impl<B>>` specialization inside  
```  
  
See https://github.com/boostorg/pfr/issues/100 for more detail about `BOOST_PFR_REGISTRATE_REFLECTABLE` macro and `boost::pfr::view` manipulator

---

## Review 5 [Changes requested]

> Username: apolukhin  
> Created_at: 2022-09-01 08:42:53 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/103#pullrequestreview-1092960467  

📁 include/boost/pfr/core.hpp

```diff
  77 | /// \code
```

> Username: apolukhin  
> Created_at: 2022-09-01 08:15:10 UTC  
> Updated_at: 2022-09-01 08:42:53 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960353430  

Permissions on this file changed from 644 to 755. Pleasse do chmod -x

> Username: denzor200  
> Created_at: 2022-09-01 13:50:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960681931  

I supose it's WSL-side trouble..  
Fixed by `chmod 644 include/boost/pfr/core.hpp && chmod 644 include/boost/pfr/tuple_size.hpp && chmod 644 test/run/tuple_like_specializable.cpp`

---

📁 include/boost/pfr/core.hpp

```diff
  84 |+ template<std::size_t I, class T>
  85 |+ struct tuple_element<I, const T> {
  86 |+     using type = std::add_const_t<typename boost::pfr::tuple_element<I, T>::type>;
```

> Username: apolukhin  
> Created_at: 2022-09-01 08:19:25 UTC  
> Updated_at: 2022-09-01 08:42:53 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960357737  

To avoid additional template instantiations:  
```diff  
-    using type = std::add_const_t<typename boost::pfr::tuple_element<I, T>::type>;  
+    using type = const typename boost::pfr::tuple_element<I, T>::type;  
```

> Username: denzor200  
> Created_at: 2022-09-01 13:51:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960682002  

Replaced to `using type = const typename boost::pfr::tuple_element<I, T>::type;`, did't work without 'typename'

---

📁 include/boost/pfr/core.hpp

```diff
  89 |+ template<std::size_t I, class T>
  90 |+ struct tuple_element<I, volatile T> {
  91 |+     using type = std::add_volatile_t<typename boost::pfr::tuple_element<I, T>::type>;
```

> Username: apolukhin  
> Created_at: 2022-09-01 08:19:46 UTC  
> Updated_at: 2022-09-01 08:42:53 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960358089  

To avoid additional template instantiations:  
```diff  
-    using type = std::add_volatile_t<typename boost::pfr::tuple_element<I, T>::type>;  
+    using type = volatile typename boost::pfr::tuple_element<I, T>::type;  
```

> Username: denzor200  
> Created_at: 2022-09-01 13:51:05 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960682066  

Resolved, did't work without 'typename'

---

📁 include/boost/pfr/core.hpp

```diff
  94 |+ template<std::size_t I, class T>
  95 |+ struct tuple_element<I, const volatile T> {
  96 |+     using type = std::add_cv_t<typename boost::pfr::tuple_element<I, T>::type>;
```

> Username: apolukhin  
> Created_at: 2022-09-01 08:20:16 UTC  
> Updated_at: 2022-09-01 08:42:53 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960358594  

To avoid additional template instantiations:  
```diff  
-    using type = std::add_cv_t<typename boost::pfr::tuple_element<I, T>::type>;  
+    using type = const volatile boost::pfr::tuple_element<I, T>::type;  
```

> Username: denzor200  
> Created_at: 2022-09-01 13:51:08 UTC  
> Updated_at: 2022-09-01 13:51:09 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960682144  

Resolved, did't work without 'typename'


📁 test/run/tuple_like_specializable.cpp

```diff
  36 |+     static_assert(boost::pfr::tuple_size_v<const volatile B> == 3, "");
  37 |+     
  38 |+     static_assert(boost::pfr::tuple_size_v<S> == 20, "");
```

> Username: apolukhin  
> Created_at: 2022-09-01 08:26:39 UTC  
> Updated_at: 2022-09-01 08:42:53 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960364953  

The more I think about it, the less I like the idea of specializing those traits.  
  
Most part of the library uses `boost::pfr::detail::fields_count<T>()` to get the size. Specializing the `tuple_size` would break random parts of the library without any diagnostics.

> Username: denzor200  
> Created_at: 2022-09-01 13:51:41 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#discussion_r960682776  

The usefulness of `tuple_size` specialization has been discussed in the PM.  
More details see docs(will be available soon) in PR of https://github.com/boostorg/pfr/issues/102


---

## Comment 6

> Username: denzor200  
> Created_at: 2022-12-16 16:27:37 UTC  
> Url: https://github.com/boostorg/pfr/pull/103#issuecomment-1355173667  

Not actual. It may be reopened in the future when we decide that we really need 'boost::pfr::view' or 'boost::pfr::view_fields', but not now.

---
