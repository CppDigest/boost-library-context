# #82 Mostly remove pre-CXX11 workarounds. [Closed]

> Username: gogagum  
> Created at: 2024-06-10 18:00:15 UTC  
> Updated at: 2025-01-26 12:57:13 UTC  
> Closed at: 2025-01-26 12:56:24 UTC  
> Url: https://github.com/boostorg/iterator/pull/82  

С++03 support [was deprecated](https://www.boost.org/users/history/version_1_85_0.html) in 1.85 and now can be removed. This PR clears many of workarounds, which are no longer needed now.  
  
- Remove unused workaround macros (many of).  
- Remove `BOOST_STATIC_ASSERT` usages.  
- Minimize Boost::type_traits dependency (in favour of STL's `type_traits`).

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-06-10 21:30:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/82#pullrequestreview-2108747934  

📁 include/boost/iterator/counting_iterator.hpp

```diff
  36 |       // For a while, this wasn't true, but we rely on it below. This is a regression assert.
  42 |-       BOOST_STATIC_ASSERT(::boost::is_integral<char>::value);
  37 |+       static_assert(std::is_integral<char>::value, "");
```

> Username: Lastique  
> Created_at: 2024-06-10 21:02:46 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633825409  

Please, provide messages in `static_assert`s. Here and everywhere else.


📁 include/boost/iterator/filter_iterator.hpp

```diff
  58 |       filter_iterator(Predicate f, Iterator x, Iterator end_ = Iterator())
  65 |-           : super_t(BOOST_ITERATOR_DETAIL_MOVE(Iterator, x)), m_predicate(BOOST_ITERATOR_DETAIL_MOVE(Predicate, f)), m_end(BOOST_ITERATOR_DETAIL_MOVE(Iterator, end_))
  59 |+           : super_t(std::move(x)), m_predicate(std::move(f)), m_end(std::move(end_))
```

> Username: Lastique  
> Created_at: 2024-06-10 21:04:08 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633826684  

Use `static_cast`s instead of `std::move` to avoid having to include `<utility>` (which is now missing anyway).


📁 include/boost/iterator/function_input_iterator.hpp

```diff
  58 |             function_object_input_iterator(Function & f_, Input state_ = Input())
  63 |-                 : f(boost::addressof(f_)), state(state_) {}
  59 |+                 : f(std::addressof(f_)), state(state_) {}
```

> Username: Lastique  
> Created_at: 2024-06-10 21:06:05 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633828272  

I think, `boost::addressof` is still preferable as it doesn't require `<memory>`. Here and everywhere else.


📁 include/boost/iterator/is_iterator.hpp

```diff
  69 |+     std::integral_constant<
  70 |+         bool
  71 |+       , boost::is_complete<T>::value && !std::is_function<T>::value
```

> Username: Lastique  
> Created_at: 2024-06-10 21:08:12 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633830143  

`boost::conjunction` is more optimal. I think, it should stay or at least be abstracted like in other libraries, e.g. like [here](https://github.com/boostorg/scope/blob/eea343a918c586387b1b717c31c0a24bc612e5c9/include/boost/scope/detail/type_traits/conjunction.hpp).  
  
Also, trailing commas are more preferable than leading commas.


📁 include/boost/iterator/iterator_adaptor.hpp

```diff
 133 |-           is_convertible<From, To>
  85 |+     : std::enable_if<
  86 |+           std::is_convertible<From, To>::value
```

> Username: Lastique  
> Created_at: 2024-06-10 21:14:10 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633835165  

Maybe convert `enable_if_convertible` to an alias template?

---

📁 include/boost/iterator/iterator_adaptor.hpp

```diff
 173 |   // iterator category has to be adjusted as well.  Any of the
 223 |-   // following four template arguments may be ommitted or explicitly
 174 |+   // following four template arguments may be committed or explicitly
```

> Username: Lastique  
> Created_at: 2024-06-10 21:15:09 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633836097  

This change is incorrect.


📁 test/detail/zip_iterator_test.ipp

```diff
   9 | #include <boost/core/lightweight_test.hpp>
  10 | 
  11 |+ #include <boost/type_traits/is_integral.hpp>
```

> Username: Lastique  
> Created_at: 2024-06-10 21:23:29 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633844168  

Why? I don't see where it is needed.


📁 test/is_iterator.cpp

```diff
 150 |+     BOOST_TEST(!boost::iterators::is_iterator< int (complete::*)(int) BOOST_NOEXCEPT >::value);
 151 |+     BOOST_TEST(!boost::iterators::is_iterator< int (complete::*)(int) const BOOST_NOEXCEPT >::value);
 152 |+ 
```

> Username: Lastique  
> Created_at: 2024-06-10 21:26:27 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633846789  

This change is incorrect. The test checks behavior with C++17 `noexcept` specifiers being part of the function type.


📁 test/range_distance_compat_test.cpp

```diff
   6 | 
   7 | #include <boost/range/distance.hpp>
   8 |+ #include <boost/type_traits/is_integral.hpp>
```

> Username: Lastique  
> Created_at: 2024-06-10 21:27:46 UTC  
> Updated_at: 2024-06-10 21:30:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1633848002  

Why?


---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2024-06-23 15:01:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/82#pullrequestreview-2134302593  

📁 test/range_distance_compat_test.cpp

```diff
   7 | #include <boost/range/distance.hpp>
   8 |- #include <boost/type_traits/is_integral.hpp>
   8 |+ #include <boost/type_traits/is_integral.hpp>  // There is a missed include in boost/range/concepts.hpp. This include must be removed in the future.
```

> Username: Lastique  
> Created_at: 2024-06-23 15:01:18 UTC  
> Updated_at: 2024-06-23 15:01:19 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1650094066  

Please file a PR for Boost.Range rather than add unnecessary includes like this. I bet noone will remember to remove this include in the future.


---

## Comment 3

> Username: Lastique  
> Created_at: 2024-06-23 15:02:57 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#issuecomment-2185031489  

Also, as a general comment, please convert this to a draft PR until you consider everything done and ready for review.

---

## Review 4 [Commented]

> Username: Lastique  
> Created_at: 2024-10-11 16:02:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/82#pullrequestreview-2362976197  

📁 include/boost/iterator/interoperable.hpp

```diff
  37 |+           bool
  38 |+         , std::is_convertible< A, B >::value || std::is_convertible< B, A >::value
  39 |+       >
```

> Username: Lastique  
> Created_at: 2024-10-11 14:40:24 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797057536  

Use `disjunction< is_convertible, is_convertible >` instead.


📁 include/boost/iterator/counting_iterator.hpp

```diff
  36 |       // For a while, this wasn't true, but we rely on it below. This is a regression assert.
  42 |-       BOOST_STATIC_ASSERT(::boost::is_integral<char>::value);
  37 |+       static_assert(std::is_integral<char>::value, "std::is_integral<char> is expected to be true");
```

> Username: Lastique  
> Created_at: 2024-10-11 14:42:10 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797059943  

I think, this can be removed now. I doubt any C++11 compiler is affected by this (and we won't support any that are).


📁 include/boost/iterator/detail/facade_iterator_category.hpp

```diff
  78 | struct iterator_facade_default_category
  79 |   : mpl::eval_if<
  80 |         mpl::and_<
```

> Username: Lastique  
> Created_at: 2024-10-11 14:44:50 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797063321  

`mpl::and_`, `mpl::or_` and `mpl::not_` can be replaced by `conjunction`, `disjunction` and `negation` wrappers. Here and everywhere else.


📁 include/boost/iterator/detail/type_traits/conjunction.hpp

```diff
  51 |+ #endif
  52 |+ 
  53 |+ #endif // BOOST_ITERATOR_DETAIL_TYPE_TRAITS_CONJUNCTION_HPP_INCLUDED_
```

> Username: Lastique  
> Created_at: 2024-10-11 14:47:44 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797067194  

Missing newline.


📁 include/boost/iterator/is_iterator.hpp

```diff
  70 |+     public conjunction<
  71 |+         boost::is_complete<T>,
  72 |+         std::integral_constant<bool, !std::is_function<T>::value>
```

> Username: Lastique  
> Created_at: 2024-10-11 14:51:08 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797071574  

Retain `negation` here. It prevents `is_function` from being instantiated, when not needed.  
  
You can create a portability header for `negation`, like for `conjunction`/`disjunction`.


📁 include/boost/iterator/iterator_adaptor.hpp

```diff
 258 |+           static_assert(
 259 |+             std::is_convertible<my_traversal, random_access_traversal_tag>::value,
 260 |+             "Super iterator must have a random_access_traversal_tag."
```

> Username: Lastique  
> Created_at: 2024-10-11 14:57:09 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797079412  

Something like this is better: "Iterator must support random access traversal." Similarly in the asserts below.


📁 include/boost/iterator/iterator_archetypes.hpp

```diff
 326 |     {
 333 |-         BOOST_STATIC_ASSERT(!is_const<Value>::value);
 327 |+         static_assert(!std::is_const<Value>::value, "Value type must be const.");
```

> Username: Lastique  
> Created_at: 2024-10-11 14:58:32 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797081314  

"...must *not* be const." Similarly below.


📁 include/boost/iterator/iterator_facade.hpp

```diff
 470 |-                 boost::is_POD<ValueType>
 437 |+                 std::is_standard_layout<ValueType>
 438 |+               , std::is_trivial<ValueType>
```

> Username: Lastique  
> Created_at: 2024-10-11 15:20:05 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797108184  

`is_standard_layout` seems irrelevant here. `is_trivially_copy_constructible` seems closer to the original code, but just `is_copy_constructible` is probably better, as the comment suggests.

---

📁 include/boost/iterator/iterator_facade.hpp

```diff
 935 |+         is_interoperable<Derived1, Derived2>::value,                            \
 936 |+         "Derived1 & Derived2 types must be interoperable."                      \
 937 |+       );                                                                        \
```

> Username: Lastique  
> Created_at: 2024-10-11 15:31:47 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797122529  

I suspect, this should not be needed as we won't support compilers without SFINAE. Perhaps, after removal of `BOOST_NO_STRICT_ITERATOR_INTEROPERABILITY` this assert will no longer be relevant.

---

📁 include/boost/iterator/iterator_facade.hpp

```diff
 969 |+           is_traversal_at_least<Derived2IterCat, random_access_traversal_tag>::value,   \
 970 |+           "Derived1 & Derived2 types must be interoperable and must both have "         \
 971 |+           "random_access_traversal_tag."                                                \
```

> Username: Lastique  
> Created_at: 2024-10-11 15:32:48 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797123706  

"...and must both support random access traversal."


📁 include/boost/iterator/detail/config_def.hpp

```diff
 113 |+ #if !defined(BOOST_MSVC) && (defined(BOOST_NO_IS_CONVERTIBLE) || defined(BOOST_NO_IS_CONVERTIBLE_TEMPLATE))
 114 | # define BOOST_NO_STRICT_ITERATOR_INTEROPERABILITY
 115 | #endif
```

> Username: Lastique  
> Created_at: 2024-10-11 15:28:17 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797118237  

Are these all still needed?


📁 include/boost/iterator/minimum_category.hpp

```diff
  46 |+             std::is_same<T1,T2>::value,
  47 |+             "Types must be same when they are convertible to each other."
  48 |+         );
```

> Username: Lastique  
> Created_at: 2024-10-11 15:36:12 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797127547  

Something like this makes more sense: "Iterator category types must be the same when they are equivalent."


📁 include/boost/iterator/new_iterator_tests.hpp

```diff
 132 |-   BOOST_STATIC_ASSERT(is_lvalue_iterator<Iterator>::value);
 133 |-   BOOST_STATIC_ASSERT(!is_non_const_lvalue_iterator<Iterator>::value);
 133 |+   static_assert(is_lvalue_iterator<Iterator>::value, "Iterator must be lvalue.");
```

> Username: Lastique  
> Created_at: 2024-10-11 15:40:16 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797132981  

"Iterator must be an lvalue iterator." And similarly below.

---

📁 include/boost/iterator/new_iterator_tests.hpp

```diff
 133 |-   BOOST_STATIC_ASSERT(!is_non_const_lvalue_iterator<Iterator>::value);
 133 |+   static_assert(is_lvalue_iterator<Iterator>::value, "Iterator must be lvalue.");
 134 |+   static_assert(!is_non_const_lvalue_iterator<Iterator>::value, "Iterator must be const.");
```

> Username: Lastique  
> Created_at: 2024-10-11 15:40:40 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797133461  

"Iterator must be a const lvalue iterator." And similarly below.


📁 include/boost/pending/iterator_tests.hpp

```diff
 144 |+         static_assert(
 145 |+           std::is_same<reference, value_type&>::value || std::is_same<reference, const value_type&>::value,
 146 |+           "reference must either be a reference to value_type or constant reference to value_type."
```

> Username: Lastique  
> Created_at: 2024-10-11 15:43:16 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797136397  

Please, keep indentation step at 4 spaces in this file.


📁 test/indirect_iter_member_types.cpp

```diff
  41 |+     static_assert(std::is_convertible<Iter::iterator_category,
  42 |+                                       std::random_access_iterator_tag>::value,
  43 |+                   "Iter must have an STL random_access_iterator_tag.");
```

> Username: Lastique  
> Created_at: 2024-10-11 15:47:20 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797141045  

"Iterator must have a random access category." Similarly below.


📁 test/iterator_adaptor_test.cpp

```diff
 212 |+     static_assert(
 213 |+       std::is_convertible<Iter1::iterator_category, std::random_access_iterator_tag>::value,
 214 |+       "Iter1::iterator_category must be convertible to std::random_access_iterator_tag."
```

> Username: Lastique  
> Created_at: 2024-10-11 15:49:33 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797143845  

"Iterator must have a random access category."

---

📁 test/iterator_adaptor_test.cpp

```diff
 227 | # ifndef BOOST_NO_LVALUE_RETURN_DETECTION
 225 |-     BOOST_STATIC_ASSERT(boost::is_lvalue_iterator<Iter1>::value);
 228 |+     static_assert(boost::is_lvalue_iterator<Iter1>::value, "Iter1 is expected to be lvalue.");
```

> Username: Lastique  
> Created_at: 2024-10-11 15:50:04 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797144466  

"...to be an lvalue iterator."


📁 test/permutation_iterator_test.cpp

```diff
  44 |   const int index_size = 7;
  45 | 
  47 |-   BOOST_STATIC_ASSERT(index_size <= element_range_size);
```

> Username: Lastique  
> Created_at: 2024-10-11 15:57:00 UTC  
> Updated_at: 2024-10-11 16:02:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1797152361  

I think, this assert should stay for documenting expectations of the following code.


---

## Review 5 [Commented]

> Username: Lastique  
> Created_at: 2024-10-19 14:33:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/82#pullrequestreview-2379505343  

📁 include/boost/iterator/is_lvalue_iterator.hpp

```diff
  87 |       template <class It>
  89 |-       struct rebind : boost::mpl::false_
  88 |+       struct rebind : std::integral_constant<bool, false>
```

> Username: Lastique  
> Created_at: 2024-10-19 14:33:34 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1807361995  

You can use `std::false_type`/`std::true_type`. Here and everywhere else.


---

## Review 6 [Changes requested]

> Username: Lastique  
> Created_at: 2025-01-18 12:41:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/iterator/pull/82#pullrequestreview-2560410950  

Thanks, you're almost there. Only a few minor comments left.

📁 include/boost/iterator/function_input_iterator.hpp

```diff
  58 |             function_object_input_iterator(Function & f_, Input state_ = Input())
  63 |-                 : f(boost::addressof(f_)), state(state_) {}
  59 |+                 : f(addressof(f_)), state(state_) {}
```

> Username: Lastique  
> Created_at: 2025-01-18 11:53:03 UTC  
> Updated_at: 2025-01-18 12:41:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1921051123  

This should stay qualified as `boost::addressof` to avoid ADL.


📁 include/boost/iterator/iterator_facade.hpp

```diff
 818 |-               , use_proxy()
 776 |+               , std::integral_constant<bool, use_proxy>{}
 777 |             );
```

> Username: Lastique  
> Created_at: 2025-01-18 12:10:42 UTC  
> Updated_at: 2025-01-18 12:41:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1921053681  

I think, this should be better left as a `typedef` instead of introducing a local variable. Something like this:  
  
```  
using use_proxy = boost::iterators::detail::use_operator_brackets_proxy<Value, Reference>;  
  
return boost::iterators::detail::make_operator_brackets_result<Derived>(  
    ...  
    , std::integral_constant<bool, use_proxy::value>{}  
);  
```  
  
Technically, local variables, even `const` ones, are still allocated on the stack, and I would prefer `operator[]` to be as lightweight as possible.


📁 include/boost/iterator/new_iterator_tests.hpp

```diff
  53 |-     T v2(*i1++);
  54 |-     BOOST_TEST(v == v2);
  48 |+ void readable_iterator_traversal_test(Iterator i1, T v, std::true_type) {
```

> Username: Lastique  
> Created_at: 2025-01-18 12:29:11 UTC  
> Updated_at: 2025-01-18 12:41:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1921056390  

Please, keep the braces on the next line. Here and everywhere else.  
  
As a general comment, it is preferable to minimize code formatting changes for easier review. This PR is already quite large.

---

📁 include/boost/iterator/new_iterator_tests.hpp

```diff
 121 |+                 "reference type must be the same as const value_type& for "
 122 |+                 "constant lvalue iterator.");
 123 |+   const T &v2 = *i2;
```

> Username: Lastique  
> Created_at: 2025-01-18 12:30:00 UTC  
> Updated_at: 2025-01-18 12:41:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1921056481  

Please, keep the references type-adjacent. Here and everywhere else.

---

📁 include/boost/iterator/new_iterator_tests.hpp

```diff
 113 |- void swappable_iterator_test(Iterator i, Iterator j)
 114 |- {
 107 |+ template <class Iterator> void swappable_iterator_test(Iterator i, Iterator j) {
```

> Username: Lastique  
> Created_at: 2025-01-18 12:30:46 UTC  
> Updated_at: 2025-01-18 12:41:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1921056567  

Please, keep `template` header and the function/class signature on separate lines. Here and everywhere else.

---

📁 include/boost/iterator/new_iterator_tests.hpp

```diff
 229 |-   for (c = 0; c < N-1; ++c)
 230 |-   {
 223 |+   for (c = 0; c < N - 1; ++c) {
```

> Username: Lastique  
> Created_at: 2025-01-18 12:34:21 UTC  
> Updated_at: 2025-01-18 12:41:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1921057075  

Please, keep the opening braces on the next line. Here and everywhere else.


📁 test/static_assert_same.hpp

```diff
   9 | 
  11 |- #define STATIC_ASSERT_SAME( T1,T2 ) BOOST_STATIC_ASSERT((::boost::is_same< T1, T2 >::value))
  10 |+ #define STATIC_ASSERT_SAME( T1,T2 ) static_assert(std::is_same<T1, T2>::value, "T1 ans T2 are expected to be the same types.")
```

> Username: Lastique  
> Created_at: 2025-01-18 12:39:44 UTC  
> Updated_at: 2025-01-18 12:41:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#discussion_r1921057734  

"T1 an**d** T2..."


---

## Comment 7

> Username: Lastique  
> Created_at: 2025-01-26 12:57:12 UTC  
> Url: https://github.com/boostorg/iterator/pull/82#issuecomment-2614408239  

Merged with some amendments. Thank you for your work.

---
