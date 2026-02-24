# #12 Prototype of removing virtual calls and heap usage for small objects [Merged]

> Username: rarutyun  
> Created at: 2019-05-14 12:39:23 UTC  
> Updated at: 2021-04-17 15:10:27 UTC  
> Merged at: 2021-04-17 15:10:27 UTC  
> Closed at: 2021-04-17 15:10:27 UTC  
> Url: https://github.com/boostorg/any/pull/12  



---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2019-06-21 19:43:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/any/pull/12#pullrequestreview-252990888  

📁 include/boost/basic_any.hpp

```diff
  41 |+     {
  42 |+         BOOST_STATIC_ASSERT_MSG(OptimizeForSize > 0 && OptimizeForAlignment > 0, "Size and Align shall be positive values");
  43 |+         BOOST_STATIC_ASSERT_MSG(OptimizeForSize >= OptimizeForAlignment, "Size shall non less than Align");
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:16:50 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296363070  

Size must be greater than Align

> Username: rarutyun  
> Created_at: 2019-12-01 17:47:02 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352361090  

Size may be equal or greater than alignment

---

📁 include/boost/basic_any.hpp

```diff
  75 |+                 case AnyCast:
  76 |+                     return boost::typeindex::type_id<ValueType>() == *info ?
  77 |+                             reinterpret_cast<BOOST_DEDUCED_TYPENAME remove_cv<ValueType>::type *>(&left.content.small_value) : 0;
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:19:06 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296363677  

BOOST_DEDUCED_TYPENAME is meant for ancient MSVC, that had problems with templates. I do not think that we should support a two decades old compiler in this new implementation.  
  
So just use `typename`

> Username: rarutyun  
> Created_at: 2019-12-01 17:53:12 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352361431  

done

---

📁 include/boost/basic_any.hpp

```diff
 117 |+ 
 118 |+         template <typename ValueType>
 119 |+         struct is_small_object_impl : boost::integral_constant<bool, sizeof(ValueType) <= OptimizeForSize &&
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:21:26 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296364333  

Drop the is_small_object down below. Rename is_small_object_impl to is_small_object

> Username: rarutyun  
> Created_at: 2019-12-01 18:06:02 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352362042  

Done

---

📁 include/boost/basic_any.hpp

```diff
 140 |+         }
 141 |+ 
 142 |+ #ifndef BOOST_NO_CXX11_RVALUE_REFERENCES
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:25:07 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296365366  

You have used `std::move` at line 66, so the code would not work on pre-C++11 compilers. I'd recommend concentrate on C++11 and above standards. So just assume that all the `BOOST_NO_CXX11_*` macro are not defined. Just remove the checks, simplify the code :)

> Username: rarutyun  
> Created_at: 2019-12-01 18:15:13 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352362579  

As we discussed for potential future replacement of boost::any, let's keep c++98 workability. Also fixed the "default template arguments for functions" issue

---

📁 include/boost/basic_any.hpp

```diff
 166 |+         }
 167 |+ template <typename T>
 168 |+ struct print;
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:25:50 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296365581  

Not used. Remove

> Username: rarutyun  
> Created_at: 2019-12-01 18:15:56 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352362616  

yep. Debug stuff. Done

---

📁 include/boost/basic_any.hpp

```diff
 295 |+         }
 296 |+ 
 297 |+ #ifndef BOOST_NO_MEMBER_TEMPLATE_FRIENDS
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:27:10 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296366062  

Kill that. Let's assume that BOOST_NO_MEMBER_TEMPLATE_FRIENDS is never defined.

> Username: rarutyun  
> Created_at: 2019-12-01 18:19:28 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352362801  

done

---

📁 include/boost/basic_any.hpp

```diff
 431 |+ }
 432 |+ 
 433 |+ // Copyright Kevlin Henney, 2000, 2001, 2002. All rights reserved.
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:28:32 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296366512  

Move this to top, put in your name and current year.

> Username: rarutyun  
> Created_at: 2019-12-01 18:43:18 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352364194  

Add my name and year. It's on the bottom for consistency with any.hpp. Are you sure that I need to move it on the top?

> Username: rarutyun  
> Created_at: 2021-03-27 18:13:48 UTC  
> Updated_at: 2021-03-27 18:13:57 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r602775159  

done

---

📁 include/boost/basic_any.hpp

```diff
 161 |+         public: // structors
 162 |+ 
 163 |+         basic_any() BOOST_NOEXCEPT
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:36:18 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296368740  

Add constexpr to force static initialization for default constructed any.

> Username: rarutyun  
> Created_at: 2019-12-01 18:29:41 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352363343  

Done

---

📁 include/boost/basic_any.hpp

```diff
 239 |+ 
 240 |+ #ifdef BOOST_NO_CXX11_RVALUE_REFERENCES
 241 |+         template<typename ValueType>
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:37:23 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296369074  

Kill that whole branch and just assume that we have rvalues.

> Username: rarutyun  
> Created_at: 2019-12-01 18:31:19 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352363477  

As we discussed for potential boost::any replacement let's leave composability with C++98

---

📁 include/boost/basic_any.hpp

```diff
  15 |+ // when:  July 2001, April 2013 - 2019
  16 |+ 
  17 |+ #include <algorithm>
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:39:10 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296369600  

Let's concentrate on C++11 implementation. In that case we can use a more lightweight `<utility>` header for getting the `swap` and other helpers.

> Username: rarutyun  
> Created_at: 2019-12-01 18:46:10 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352364370  

As we discussed for potential boost::any replacement let's leave composability with C++98. If you want me to add preprocessor branch to include utility for C++11 just let me know

---

📁 include/boost/basic_any.hpp

```diff
 271 |+         basic_any & operator=(ValueType&& rhs)
 272 |+         {
 273 |+             basic_any(static_cast<ValueType&&>(rhs)).swap(*this);
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:41:27 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296370263  

Just use `std::move` instead of `static_cast<ValueType&&>(rhs)`

> Username: rarutyun  
> Created_at: 2019-12-01 18:53:25 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352364766  

It's not std::move analog but rather std::forward. I also used in boost::any. As I understand it's necessary to support compilers that don't implement std::forward yet but have support for Forwarding reference on the language level.  
  
If you want me to change std::move for some specific language cast in Move operation just let me know


📁 test/basic_any_cast_cv_failed.cpp

```diff
   1 |+ // Copyright 2006 Alexander Nasonov.
   2 |+ // Copyright Antony Polukhin, 2013-2019.
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:29:08 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296366697  

Add your name on a new line in every file

> Username: rarutyun  
> Created_at: 2021-03-27 18:13:06 UTC  
> Updated_at: 2021-03-27 18:13:57 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r602775061  

Done.


📁 test/basic_any_test_rv.cpp

```diff
  41 |+ }
  42 |+ 
  43 |+ namespace any_tests // test suite
```

> Username: apolukhin  
> Created_at: 2019-06-21 19:34:21 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r296368216  

Hmmm. Too much copy-pasting.  
  
Change this file to a header, convert the any_tests to a templated structure with template parameter `class Any`. Use that header for testing the old `boost::any` and the new one.

> Username: rarutyun  
> Created_at: 2019-12-01 22:33:39 UTC  
> Updated_at: 2021-03-27 18:11:39 UTC  
> Url: https://github.com/boostorg/any/pull/12#discussion_r352378402  

I completely agree that we need to avoid code duplication in this place. I made the common header just for move (rv) tests for both basic_any and any to verify the approach. The only thing I don't like there and the ANY_CAST_OP define. I faced with problem to pass "basic_any_cast" or "any_cast" without higher code modifications when templatized the common class. If we are ok with the approach I may also consider to do the common header for another big test. Otherwise let's think about something better.


---
