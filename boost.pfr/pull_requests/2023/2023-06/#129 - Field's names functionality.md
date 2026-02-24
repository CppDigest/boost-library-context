# #129 Field's names functionality [Merged]

> Username: denzor200  
> Created at: 2023-06-25 22:48:53 UTC  
> Updated at: 2025-12-01 12:55:03 UTC  
> Merged at: 2023-09-17 14:54:44 UTC  
> Closed at: 2023-09-17 14:54:44 UTC  
> Url: https://github.com/boostorg/pfr/pull/129  

link for manual testing: https://godbolt.org/z/xbo7bos86

---

## Review 1 [Commented]

> Username: schaumb  
> Created_at: 2023-06-26 10:44:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1498174758  

📁 include/boost/pfr/detail/core_name.hpp

```diff
  44 |+     for (auto a = first+1; a <= last; ++a)
  45 |+         *it++ = sv[a];
  46 |+     return res;
```

> Username: schaumb  
> Created_at: 2023-06-26 10:23:05 UTC  
> Updated_at: 2023-06-26 10:44:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1241966495  

The member name can contain non-ascii characters as identifiers (when it's enabled by the compiler). This function should handle it.

---

📁 include/boost/pfr/detail/core_name.hpp

```diff
  31 |+ 
  32 |+ template <auto& ptr> 
  33 |+ constexpr auto name_of_field_impl() noexcept {
```

> Username: schaumb  
> Created_at: 2023-06-26 10:24:00 UTC  
> Updated_at: 2023-06-26 10:44:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1241967410  

make it consteval. This "hack" works only at C++20 standard, and the compiler will generate the full string  `__FUNCSIG__` or `__PRETTY_FUNCTION__` into the binary when it's not consteval.

---

📁 include/boost/pfr/detail/core_name.hpp

```diff
  42 |+     std::array<char, last - first + 1> res{};
  43 |+     auto it = res.data();
  44 |+     for (auto a = first+1; a <= last; ++a)
```

> Username: schaumb  
> Created_at: 2023-06-26 10:42:36 UTC  
> Updated_at: 2023-06-26 10:44:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1241986804  

If `std:[:ranges:]:copy` is constexpr, that can be used replacement of this `for`


📁 include/boost/pfr/core_name.hpp

```diff
  31 |+ constexpr std::string_view get_name() noexcept {
  32 |+     static_assert(sizeof(T) && BOOST_PFR_ENABLE_GETTING_NAMES, "====================> Boost.PFR: Calling boost::pfr::get_name is allowed only in C++20");
  33 |+     return detail::sequence_tuple::get<I>( detail::tie_as_names_tuple<T>() );
```

> Username: schaumb  
> Created_at: 2023-06-26 10:32:01 UTC  
> Updated_at: 2023-06-26 10:44:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1241975727  

This will put all member name into the binary. If the developer want to use only for 1 member name of a big struct, the binary can increase unnecessarily


📁 include/boost/pfr/config.hpp

```diff
  99 | #endif
 100 | 
 101 |+ #ifndef BOOST_PFR_ENABLE_GETTING_NAMES
```

> Username: schaumb  
> Created_at: 2023-06-26 10:40:02 UTC  
> Updated_at: 2023-06-26 10:44:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1241984200  

It need to check that `__FUNCSIG__` or `__PRETTY_FUNCTION__` is exists [[link]](https://github.com/boostorg/assert/blob/develop/include/boost/current_function.hpp#L35)


---

## Comment 2

> Username: coveralls  
> Created_at: 2023-08-12 19:40:56 UTC  
> Updated_at: 2025-12-01 12:55:03 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#issuecomment-1676072299  

## Pull Request Test Coverage Report for [Build 5843075514](https://coveralls.io/builds/61949503)  
  
### Warning: This coverage report may be inaccurate.  
  
This pull request's base commit is no longer the HEAD commit of its target branch. This means it includes changes from outside the original pull request, including, potentially, unrelated coverage changes.  
  
- For more information on this, see <a target="_blank" href="https://docs.coveralls.io/build-types#tracking-coverage-changes-with-pull_request-builds">Tracking coverage changes with pull request builds</a>.  
- To avoid this issue with future PRs, see these <a target="_blank" href="https://docs.coveralls.io/build-types#recommended-ci-configurations">Recommended CI Configurations</a>.  
- For a quick fix, <a target="_blank" href="https://github.blog/changelog/2022-02-03-more-ways-to-keep-your-pull-request-branch-up-to-date/#update-your-pull-request-branch-by-rebasing">rebase this PR at GitHub</a>. Your next report should be accurate.  
  
### Details  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/61949503/badge)](https://coveralls.io/builds/61949503) |  
| :-- | --: |  
| Change from base [Build 5840157514](https://coveralls.io/builds/61947561): |  0.0% |  
| Covered Lines: | 397 |  
| Relevant Lines: | 397 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Review 3 [Commented]

> Username: denzor200  
> Created_at: 2023-08-12 23:56:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1575497235  

📁 doc/pfr.qbk

```diff
 488 |     [[*BOOST_PFR_HAS_GUARANTEED_COPY_ELISION*] [Define to `0` if your compiler does not implement C++17 guaranteed copy elision properly and fails to reflect aggregates with non-movable fields. Define to `1` to override Boost.PFR detection logic. ]]
 489 |     [[*BOOST_PFR_ENABLE_IMPLICIT_REFLECTION*] [Define to `0` if you are hit by lots of non-effective choices made by implicitly reflection. Define to `1` to override Boost.PFR detection logic. ]]
 490 |+     [[*BOOST_PFR_ENABLE_GETTING_NAMES*] [On platforms where field's names extracting is not supported, the 'boost/pfr/config.hpp' header defines the BOOST_PFR_ENABLE_GETTING_NAMES macro equal to 0. Defining this macro as 0 before including the header disables the ability to get a field's name. ]]
```

> Username: denzor200  
> Created_at: 2023-08-12 23:31:01 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292543577  

Consider renaming this macro to `BOOST_PFR_ENABLE_GET_NAME_STATIC`, for example


📁 include/boost/pfr/config.hpp

```diff
 101 |+ #ifndef BOOST_PFR_FUNCTION_MACRO_SUPPORTED
 102 |+ #   if defined(__clang__) || defined(__GNUC__) || defined(_MSC_VER)
 103 |+ #       define BOOST_PFR_FUNCTION_MACRO_SUPPORTED 1
```

> Username: denzor200  
> Created_at: 2023-08-12 23:33:20 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292543838  

Inline this macro

---

📁 include/boost/pfr/config.hpp

```diff
 108 |+ 
 109 |+ #ifndef BOOST_PFR_ENABLE_GETTING_NAMES
 110 |+ #   if defined(__cpp_nontype_template_args) && __cpp_nontype_template_args >= 201911 && BOOST_PFR_FUNCTION_MACRO_SUPPORTED
```

> Username: denzor200  
> Created_at: 2023-08-12 23:48:27 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292548907  

Implement workaround the clang compiler which doesn't support `__cpp_nontype_template_args >= 201911`


📁 include/boost/pfr/core_name.hpp

```diff
  28 |+ 
  29 |+ template <std::size_t I, class T>
  30 |+ constexpr auto get_name() noexcept {
```

> Username: denzor200  
> Created_at: 2023-08-12 23:35:33 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292544403  

Add docs for the public interface and one example for the short examples for the impatient


📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  25 |+ namespace boost { namespace pfr { namespace detail {
  26 |+ 
  27 |+ // TODO: move it outside
```

> Username: denzor200  
> Created_at: 2023-08-12 23:36:46 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292544503  

resolve it

---

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  49 |+ 
  50 |+ template <typename T>
  51 |+ extern const T fake_object;
```

> Username: denzor200  
> Created_at: 2023-08-12 23:41:38 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292546086  

Consider moving it to the separate file

> Username: denzor200  
> Created_at: 2023-08-12 23:59:45 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292557825  

looks like it might be helpful here https://github.com/boostorg/pfr/pull/77

> Username: denzor200  
> Created_at: 2023-08-26 22:01:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1306570351  

Of course, it might be helpful: https://godbolt.org/z/1eW5P4zh3

---

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  86 |+     );
  87 |+ 
  88 |+     return tie_as_names_tuple_impl<T>(detail::make_index_sequence<detail::fields_count<T>()>{});
```

> Username: denzor200  
> Created_at: 2023-08-12 23:53:10 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292554378  

`detail::tie_as_names_tuple_impl` to avoid ADL

---

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  78 |+ constexpr auto tie_as_names_tuple() noexcept {
  79 |+     static_assert(
  80 |+         !std::is_union<T>::value,
```

> Username: denzor200  
> Created_at: 2023-08-12 23:53:37 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292554396  

Add compile failed test for it


📁 include/boost/pfr/detail/core_name14_disabled.hpp

```diff
  41 |+     );
  42 |+ 
  43 |+     return detail::make_sequence_tuple();
```

> Username: denzor200  
> Created_at: 2023-08-12 23:39:51 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292545938  

Remove unessessary space at EOL(here and everywhere)


📁 include/boost/pfr/detail/stdarray.hpp

```diff
  25 |+ template <class T, std::size_t... I>
  26 |+ constexpr auto make_stdarray_from_tietuple(const T& t, std::index_sequence<I...>) noexcept {
  27 |+     return make_stdarray(
```

> Username: denzor200  
> Created_at: 2023-08-12 23:43:50 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292547567  

`detail::make_stdarray` to avoid ADL


📁 misc/strip_boost_namespace.sh

```diff
  41 | echo -n "***** Testing: "
  42 |- if g++-9 -std=c++17 -DPFR_USE_LOOPHOLE=0 -DPFR_USE_CPP17=1 -I ${TARGET_PATH}/include/ ${TARGET_PATH}/example/motivating_example0.cpp && ./a.out > /dev/null; then
  42 |+ if g++-12 -std=c++2a -DPFR_USE_LOOPHOLE=0 -DPFR_USE_CPP17=1 -DPFR_ENABLE_GETTING_NAMES=1 -I ${TARGET_PATH}/include/ ${TARGET_PATH}/example/motivating_example0.cpp && ./a.out > /dev/null; then
```

> Username: denzor200  
> Created_at: 2023-08-12 23:46:24 UTC  
> Updated_at: 2023-08-12 23:56:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292548163  

`g++-12 -std=c++2a -DPFR_ENABLE_GETTING_NAMES=1`, same for zero value. Disable `PFR_ENABLE_GETTING_NAMES` from the old test case


---

## Review 4 [Commented]

> Username: denzor200  
> Created_at: 2023-08-13 00:06:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1575517196  

📁 include/boost/pfr/detail/core_name14_disabled.hpp

```diff
  18 |+ namespace boost { namespace pfr { namespace detail {
  19 |+ 
  20 |+ // TODO: move it outside
```

> Username: denzor200  
> Created_at: 2023-08-13 00:04:17 UTC  
> Updated_at: 2023-08-13 00:06:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1292559708  

resolve it


---

## Review 5 [Commented]

> Username: denzor200  
> Created_at: 2023-08-18 00:39:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1583671846  

📁 include/boost/pfr/detail/core_name14_disabled.hpp

```diff
  28 |+     static_assert(
  29 |+         sizeof(T) && false,
  30 |+         "====================> Boost.PFR: Field's names extracting functionality requires C++20 and compiler that supports __PRETTY_FUNCTION__ or __FUNCSIG__ macro (GCC, Clang or MSVC)."
```

> Username: denzor200  
> Created_at: 2023-08-18 00:28:49 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297873449  

Add compile failed test for it

---

📁 include/boost/pfr/detail/core_name14_disabled.hpp

```diff
  38 |+     static_assert(
  39 |+         sizeof(T) && false,
  40 |+         "====================> Boost.PFR: Field's names extracting functionality requires C++20 and compiler that supports __PRETTY_FUNCTION__ or __FUNCSIG__ macro (GCC, Clang or MSVC)."
```

> Username: denzor200  
> Created_at: 2023-08-18 00:28:58 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297873501  

Add compile failed test for it


📁 include/boost/pfr/detail/stdarray.hpp

```diff
  11 |+ 
  12 |+ #include <utility> // metaprogramming stuff
  13 |+ #include <tuple>
```

> Username: denzor200  
> Created_at: 2023-08-18 00:29:55 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297873840  

`#include <array>`


📁 doc/pfr.qbk

```diff
 184 |+ * access to member's names by index
 185 | * member type retrieval
 186 | * methods for cooperation with `std::tuple`
```

> Username: denzor200  
> Created_at: 2023-08-18 00:31:49 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297874562  

* methods for cooperation with `std::tuple` for members

---

📁 doc/pfr.qbk

```diff
 478 | The library may work with aggregates that don't satisfy the requirements of `SimpleAggregate`, but the behavior tends to be non-portable.
 479 | 
 480 |+ Boost.PFR's extraction of field name works with a `SimpleAggregate` which variables are able to be declared in any other translation unit.
```

> Username: denzor200  
> Created_at: 2023-08-18 00:35:54 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297876002  

looks like we should raise recoomended C++ standart to C++20 everywhere in the docs

---

📁 doc/pfr.qbk

```diff
 182 | * IO streaming
 183 | * access to members by index or type
 184 |+ * access to member's names by index
```

> Username: denzor200  
> Created_at: 2023-08-18 00:36:53 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297876346  

Make a separete section "Reflection of field names" in the docs, provide more details there


📁 include/boost/pfr/core_name.hpp

```diff
  29 |+ template <std::size_t I, class T>
  30 |+ constexpr auto get_name() noexcept {
  31 |+     return detail::get_name<T, I>();
```

> Username: denzor200  
> Created_at: 2023-08-18 00:33:12 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297875098  

Ensure that returned name is not empty, add `static_assert` for it. Same for `names_as_array` below

> Username: denzor200  
> Created_at: 2023-09-09 22:26:16 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1320660765  

stayed as it is


📁 test/core_name/Jamfile.v2

```diff
  48 |+ ########## BEGIN of helpers to prepare the test with non-ascii field name
  49 |+ 
  50 |+ # TODO: fix it
```

> Username: denzor200  
> Created_at: 2023-08-18 00:33:43 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297875258  

Resolve it


📁 test/core_name/fields_names.cpp

```diff
  23 |+ };
  24 |+ 
  25 |+ struct Aggregate {
```

> Username: denzor200  
> Created_at: 2023-08-18 00:34:30 UTC  
> Updated_at: 2023-08-18 00:39:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1297875522  

Make a big test that might contain a lot of structures with a lot of fields


---

## Review 6 [Commented]

> Username: denzor200  
> Created_at: 2023-08-19 13:16:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1585640324  

📁 test/core_name/Jamfile.v2

```diff
  34 |+ ########## END of helpers to detect C++20 non-type template args support
  35 |+ 
  36 |+ local REQUIRE_CXX20_NONTYPE_TEMPLATE_ARGS =
```

> Username: denzor200  
> Created_at: 2023-08-19 13:16:16 UTC  
> Updated_at: 2023-08-19 13:16:22 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1299185651  

looks like you should remove `REQUIRE_CXX20_NONTYPE_TEMPLATE_ARGS`


---

## Review 7 [Commented]

> Username: denzor200  
> Created_at: 2023-08-24 23:04:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1594487593  

📁 include/boost/pfr/core_name.hpp

```diff
  39 |+ 
  40 |+ template <class T>
  41 |+ constexpr auto names_as_array() noexcept {
```

> Username: denzor200  
> Created_at: 2023-08-24 21:26:06 UTC  
> Updated_at: 2023-08-24 23:04:52 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1304886868  

Add test for `names_as_array` with empty struct


📁 test/core_name/generate_fields_names_big.cpp.py

```diff
  15 |+ import random
  16 |+ 
  17 |+ STRUCT_COUNT = 100
```

> Username: denzor200  
> Created_at: 2023-08-24 23:04:42 UTC  
> Updated_at: 2023-08-24 23:04:52 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1304953267  

looks like it should be sinchronized with `generate_cpp17.py` argument via `config.hpp`

> Username: denzor200  
> Created_at: 2023-08-26 22:47:53 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1306573745  

not actual anymore


---

## Review 8 [Commented]

> Username: denzor200  
> Created_at: 2023-08-25 23:54:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1596616008  

📁 test/core_name/generate_fields_names_big.cpp.py

```diff
  87 |+ ############################################################################################################################
  88 |+ 
  89 |+ field_names = {}
```

> Username: denzor200  
> Created_at: 2023-08-25 23:54:11 UTC  
> Updated_at: 2023-08-25 23:54:17 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1306227564  

Add all C++20 keywords into `field_names`


---

## Review 9 [Commented]

> Username: denzor200  
> Created_at: 2023-08-28 23:11:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1599245451  

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  78 |+ 
  79 |+ consteval std::string_view name_of_field_parse(std::string_view sv) noexcept {
  80 |+ #if defined(BOOST_PFR_CORE_NAME_USER_DEFINED_PARSING)
```

> Username: denzor200  
> Created_at: 2023-08-28 23:08:24 UTC  
> Updated_at: 2023-08-28 23:11:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1308040410  

`BOOST_PFR_CORE_NAME_USER_DEFINED_PARSING`  - integrate this macro completely, ala `BOOST_PFR_ENABLE_GET_NAME_STATIC`.  
Check it in the `config.hpp`, write one note in the doc "Limitation and Configuration" about it, print it in the `print_config.cpp`

---

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  99 |+ consteval auto name_of_field_impl() noexcept {
 100 |+     
 101 |+ #if defined(BOOST_PFR_FUNCTION_SIGNATURE)
```

> Username: denzor200  
> Created_at: 2023-08-28 23:08:56 UTC  
> Updated_at: 2023-08-28 23:11:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1308040686  

`BOOST_PFR_FUNCTION_SIGNATURE`  - integrate this macro completely, ala `BOOST_PFR_ENABLE_GET_NAME_STATIC`.  
Check it in the `config.hpp`, write one note in the doc "Limitation and Configuration" about it, print it in the `print_config.cpp`

---

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  62 |+         "PFRs extraction of field name is misconfigured for your compiler. "
  63 |+         "Please define BOOST_PFR_CORE_NAME_USER_DEFINED_PARSING to correct values. See section "
  64 |+         "'TODO: name of the section' of the documentation for more information."
```

> Username: denzor200  
> Created_at: 2023-08-28 23:11:14 UTC  
> Updated_at: 2023-08-28 23:11:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1308041801  

resolve it


📁 include/boost/pfr/config.hpp

```diff
 102 |+ #   if (defined(__cpp_nontype_template_args) && __cpp_nontype_template_args >= 201911) || (__cplusplus >= 202002L && defined(__clang_major__) && __clang_major__ >= 12)
 103 |+ // Only these 3 compilers have a macro to extract func name
 104 |+ #       if defined(__clang__) || defined(__GNUC__) || defined(_MSC_VER)
```

> Username: denzor200  
> Created_at: 2023-08-28 23:10:10 UTC  
> Updated_at: 2023-08-28 23:11:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1308041236  

not actual anymore, remove it!


---

## Review 10 [Commented]

> Username: denzor200  
> Created_at: 2023-08-30 21:04:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1603493673  

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
 150 |+ // Without passing 'T' into 'name_of_field_impl' different fields from different structures might have the same name!
 151 |+ template <class T, std::size_t I>
 152 |+ constexpr auto stored_name_of_field = name_of_field_impl<T, clang_workaround(&detail::sequence_tuple::get<I>(
```

> Username: denzor200  
> Created_at: 2023-08-30 21:01:54 UTC  
> Updated_at: 2023-08-30 21:04:25 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1310820645  

`&detail::sequence_tuple::get<I>(` -> `std::addressof(detail::sequence_tuple::get<I>(`


---

## Review 11 [Commented]

> Username: denzor200  
> Created_at: 2023-08-30 22:03:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1603562786  

📁 include/boost/pfr/config.hpp

```diff
  99 | #endif
 100 | 
 101 |+ #ifndef BOOST_PFR_ENABLE_GET_NAME_STATIC
```

> Username: denzor200  
> Created_at: 2023-08-30 22:03:28 UTC  
> Updated_at: 2023-08-30 22:03:35 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1310879027  

consider renaming to `BOOST_PFR_CORE_NAME_ENABLED`


---

## Review 12 [Commented]

> Username: denzor200  
> Created_at: 2023-09-02 13:36:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1608274059  

📁 include/boost/pfr/config.hpp

```diff
 101 |+ #ifndef BOOST_PFR_ENABLE_GET_NAME_STATIC
 102 |+ #   if (defined(__cpp_nontype_template_args) && __cpp_nontype_template_args >= 201911) \
 103 |+      || (__cplusplus >= 202002L && defined(__clang_major__) && __clang_major__ >= 12)
```

> Username: denzor200  
> Created_at: 2023-09-02 13:36:15 UTC  
> Updated_at: 2023-09-02 13:36:20 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1313830871  

It's better to check `__cplusplus >= 202002L` in both variants


---

## Review 13 [Commented]

> Username: denzor200  
> Created_at: 2023-09-02 21:08:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1608380173  

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  61 |+     static_assert(
  62 |+         Condition,
  63 |+         "PFRs extraction of field name is misconfigured for your compiler. "
```

> Username: denzor200  
> Created_at: 2023-09-02 21:07:31 UTC  
> Updated_at: 2023-09-02 21:08:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1313990720  

static_assert's message must be in Boost PFR style, ala `====================> Boost.PFR: Extraction of field name is misconfigured for your compiler. `

---

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
  71 |+     static_assert(
  72 |+         sizeof(T) && false,
  73 |+         "PFRs extraction of field name could not detect your compiler. "
```

> Username: denzor200  
> Created_at: 2023-09-02 21:07:53 UTC  
> Updated_at: 2023-09-02 21:08:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1313990825  

static_assert's message must be in Boost PFR style, ala `====================> Boost.PFR: Extraction of field name could not detect your compiler. `


---

## Review 14 [Commented]

> Username: denzor200  
> Created_at: 2023-09-03 12:04:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1608510251  

📁 doc/pfr.qbk

```diff
 583 |+ 
 584 |+ # get the output of `boost::pfr::get_name<0, A::A>()`
 585 |+ # define [macroref BOOST_PFR_CORE_NAME_PARSING] to
```

> Username: denzor200  
> Created_at: 2023-09-03 11:57:31 UTC  
> Updated_at: 2023-09-03 12:04:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1314242164  

`[macroref BOOST_PFR_CORE_NAME_PARSING]` -> `BOOST_PFR_CORE_NAME_PARSING`  
Here and everywhere in the docs. Same for another macroes

---

📁 doc/pfr.qbk

```diff
 457 |+ [section Reflection of field names ]
 458 |+ 
 459 |+ [pfr_example_get_name]
```

> Username: denzor200  
> Created_at: 2023-09-03 11:57:55 UTC  
> Updated_at: 2023-09-03 12:04:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1314242204  

It doesn't work, fix it

---

📁 doc/pfr.qbk

```diff
 510 |+ Try using `-fpermissive` if you have any issue with it.
 511 |+ 
 512 |+ As you see above extraction of field name feature has requirements of input type, additionally it has some requirements of compiler, see [link boost_pfr.limitations_of_field_names_reflection [*Limitations of field names reflection]] for details.
```

> Username: denzor200  
> Created_at: 2023-09-03 11:59:10 UTC  
> Updated_at: 2023-09-03 12:04:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1314242387  

The link is broken, fix it.  
Here and everywhere in the docs


📁 include/boost/pfr/core_name.hpp

```diff
  44 |+ /// \endcode
  45 |+ template <std::size_t I, class T>
  46 |+ constexpr auto get_name() noexcept {
```

> Username: denzor200  
> Created_at: 2023-09-03 12:00:30 UTC  
> Updated_at: 2023-09-03 12:04:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1314242530  

In the docs it must be printed as `std::string_view get_name()`, not as `auto get_name()`

---

📁 include/boost/pfr/core_name.hpp

```diff
  63 |+ /// \endcode
  64 |+ template <class T>
  65 |+ constexpr auto names_as_array() noexcept {
```

> Username: denzor200  
> Created_at: 2023-09-03 12:01:54 UTC  
> Updated_at: 2023-09-03 12:04:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1314242768  

In the docs it must be printed as `std::array<std::string_view, boost::pfr::tuple_size_v<T>> names_as_array()`, not as `auto names_as_array()`

---

📁 include/boost/pfr/core_name.hpp

```diff
  58 |+ /// \b Example:
  59 |+ /// \code
  60 |+ ///     struct my_struct { int i, short s; };
```

> Username: denzor200  
> Created_at: 2023-09-03 12:04:07 UTC  
> Updated_at: 2023-09-03 12:04:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1314243085  

`std::array<std::string_view, 2> t = boost::pfr::names_as_array<my_struct>();`


---

## Review 15 [Commented]

> Username: denzor200  
> Created_at: 2023-09-03 12:05:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1608511411  

📁 include/boost/pfr/config.hpp

```diff
 123 |+ #   if defined(_MSC_VER)
 124 |+ // sizeof("auto __cdecl boost::pfr::detail::name_of_field_impl<") - 1, sizeof(">(void) noexcept") - 1
 125 |+ #       define BOOST_PFR_CORE_NAME_PARSING (52, 16, backward("->"))
```

> Username: denzor200  
> Created_at: 2023-09-03 12:05:51 UTC  
> Updated_at: 2023-09-03 12:05:59 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1314243348  

It will definitely break `strip_boost_namespace.sh`, fix it


---

## Review 16 [Commented]

> Username: denzor200  
> Created_at: 2023-09-05 22:57:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/129#pullrequestreview-1610347041  

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
 148 |+ clang_workaround_t(T) -> clang_workaround_t<T>;
 149 |+ 
 150 |+ template<typename T>
```

> Username: denzor200  
> Created_at: 2023-09-05 06:59:52 UTC  
> Updated_at: 2023-09-05 22:57:30 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1315454768  

`template <class T>`  
Here and everywhere

---

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
 179 |+ // See https://developercommunity.visualstudio.com/t/__FUNCSIG__-outputs-wrong-value-with-C/10458554 for details
 180 |+ template <class T, std::size_t I>
 181 |+ constexpr auto stored_name_of_field = name_of_field_impl<T, make_clang_wrapper(&detail::sequence_tuple::get<I>(
```

> Username: denzor200  
> Created_at: 2023-09-05 22:56:17 UTC  
> Updated_at: 2023-09-05 22:57:30 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1316497099  

`make_clang_wrapper` -> `detail::make_clang_wrapper`   
to avoid ADL


📁 test/core_name/cxx20_clang_workaround_detection.cpp

```diff
   1 |+ // Copyright (c) 2023 Bela Schaum, X-Ryl669, Denis Mikhailov.
```

> Username: denzor200  
> Created_at: 2023-09-05 22:57:21 UTC  
> Updated_at: 2023-09-05 22:57:30 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1316497616  

Looks like you should move this detector into `cxx20_nontype_template_args_detection.cpp`

> Username: denzor200  
> Created_at: 2023-09-09 23:53:35 UTC  
> Url: https://github.com/boostorg/pfr/pull/129#discussion_r1320667131  

Wasn't moved but renamed both detectors


---
