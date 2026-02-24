# #316 is_mutable_string takes one argument [Closed]

> Username: alandefreitas  
> Created at: 2022-08-03 18:07:07 UTC  
> Updated at: 2022-08-08 14:38:51 UTC  
> Closed at: 2022-08-04 04:20:25 UTC  
> Url: https://github.com/boostorg/url/pull/316  

fix #242

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 18:08:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/316#pullrequestreview-1060798816  

📁 include/boost/url/grammar/detail/type_traits.hpp

```diff
  86 |+     operator->()
  87 |+     {
  88 |+         return &(operator*());
```

> Username: vinniefalco  
> Created_at: 2022-08-03 18:08:06 UTC  
> Updated_at: 2022-08-03 18:08:07 UTC  
> Url: https://github.com/boostorg/url/pull/316#discussion_r936990567  

leave out all the definitions please


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 18:10:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/316#pullrequestreview-1060801766  

📁 include/boost/url/grammar/detail/type_traits.hpp

```diff
  10 |+ #ifndef BOOST_URL_GRAMMAR_DETAIL_TYPE_TRAITS_HPP
  11 |+ #define BOOST_URL_GRAMMAR_DETAIL_TYPE_TRAITS_HPP
  12 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-08-03 18:10:41 UTC  
> Updated_at: 2022-08-03 18:10:42 UTC  
> Url: https://github.com/boostorg/url/pull/316#discussion_r936992663  

missing `<iterator>` and `<cstddef>`


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-08-03 18:11:18 UTC  
> Updated_at: 2022-08-03 18:11:57 UTC  
> Url: https://github.com/boostorg/url/pull/316#issuecomment-1204310608  

You could add a test, ifdef for C++20 that checks against the concept. The test would go in `test/unit/grammar/type_traits.cpp`.  
  
And.. you should check `is_mutable_string` returns true for `std::string` and false for... something else, if you haven't already (same .cpp file)

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-08-03 18:33:48 UTC  
> Url: https://github.com/boostorg/url/pull/316#issuecomment-1204332842  

Done

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 19:48:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/316#pullrequestreview-1060919036  

📁 include/boost/url/grammar/detail/type_traits.hpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)
```

> Username: vinniefalco  
> Created_at: 2022-08-03 19:48:08 UTC  
> Url: https://github.com/boostorg/url/pull/316#discussion_r937071685  

why is my name on this?


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 19:48:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/316#pullrequestreview-1060919370  

📁 include/boost/url/grammar/detail/type_traits.hpp

```diff
  32 |+ 
  33 |+     // 1.2 CopyConstructible
  34 |+     input_it_ex(input_it_ex const&) = default;
```

> Username: vinniefalco  
> Created_at: 2022-08-03 19:48:27 UTC  
> Url: https://github.com/boostorg/url/pull/316#discussion_r937072002  

technically, `=default` counts as a definition :)


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 19:48:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/316#pullrequestreview-1060919792  

📁 include/boost/url/grammar/type_traits.hpp

```diff
  30 | #else
  30 |- template<class T, class I, class = void>
  31 |+ template<class T, class = void>
```

> Username: vinniefalco  
> Created_at: 2022-08-03 19:48:53 UTC  
> Url: https://github.com/boostorg/url/pull/316#discussion_r937072469  

What about the javadoc? It still says `I`


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 19:49:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/316#pullrequestreview-1060920593  

📁 test/unit/grammar/type_traits.cpp

```diff
  25 |+     {
  26 |+ #ifdef __cpp_lib_concepts
  27 |+         BOOST_TEST(std::input_iterator<detail::input_it_ex>);
```

> Username: vinniefalco  
> Created_at: 2022-08-03 19:49:39 UTC  
> Url: https://github.com/boostorg/url/pull/316#discussion_r937073385  

Nice :)


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 19:50:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/316#pullrequestreview-1060921977  

📁 test/unit/grammar/type_traits.cpp

```diff
  28 |+ #endif
  29 |+         BOOST_TEST(is_mutable_string<std::string>::value);
  30 |+         BOOST_TEST_NOT(is_mutable_string<int>::value);
```

> Username: vinniefalco  
> Created_at: 2022-08-03 19:50:52 UTC  
> Url: https://github.com/boostorg/url/pull/316#discussion_r937074515  

c'mon man give it something to chew on, maybe `vector<char>`? or how about a `std::string const`? Or how about `std::basic_string<char const, std::char_traits<char>>` ?


---
