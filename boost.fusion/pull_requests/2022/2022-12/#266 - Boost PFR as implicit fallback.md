# #266 Boost PFR as implicit fallback [Open]

> Username: denzor200  
> Created at: 2022-12-30 07:02:26 UTC  
> Updated at: 2023-08-19 09:29:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/266  

https://github.com/boostorg/fusion/issues/231  
  
This PR suggests to use PFR in the Fusion like implicit fallback. It means that Fusion will try using PFR for types that isn't a Fusion Sequence.   
This is an optional functional enables by inclusion of special header (`adapted/boost_pfr.hpp`, for example), or it enables by inclusion of just `adapted.hpp`. If you don't need this, just don't include special header, or disable PFR using special macro( `BOOST_PFR_ENABLED`, for example ).  
  
If you need to use PFR, but you don't want to use it like implicit fallback, you may disable implicit reflection and use explicit instead. Explicit reflection requires from user to specify all reflectable types manually using one of the suggested macroes.  
Explicit reflection is provided by the header `adapted/boost_pfr_explicit.hpp`.  
  
It's able to play with this PR here: https://godbolt.org/z/vMMf8GTE4

---

## Review 1 [Commented]

> Username: denzor200  
> Created_at: 2023-01-12 09:49:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/266#pullrequestreview-1245159880  

📁 doc/adapted.qbk

```diff
   3 |     Copyright (C) 2006 Dan Marsden
   4 |     Copyright (C) 2010 Christopher Schmidt
   5 |+     Copyright (c) 2022 Denis Mikhailov
```

> Username: denzor200  
> Created_at: 2023-01-12 08:33:32 UTC  
> Updated_at: 2023-01-12 09:49:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067834414  

Please, update copyright years everywhere


📁 test/sequence/boost_pfr_empty.cpp

```diff
  44 |+ 
  45 |+     // FIXME make this workable even with forced implicit reflection
  46 |+     // std::cout << tuple_open('[');
```

> Username: denzor200  
> Created_at: 2023-01-12 08:37:51 UTC  
> Updated_at: 2023-01-12 09:49:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067838323  

Add a test to show that `decltype(tuple_open('['))` and so other Fusion's service objects won't became reflectable due to implicit reflection.

> Username: denzor200  
> Created_at: 2023-01-29 09:40:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1089915291  

There is no sense for it. We have already added regression tests, I suppose this is enough.

---

📁 test/sequence/boost_pfr_empty.cpp

```diff
   6 |+ 
   7 |+ #include <boost/detail/lightweight_test.hpp>
   8 |+ #include <boost/fusion/adapted/boost_pfr.hpp>
```

> Username: denzor200  
> Created_at: 2023-01-12 09:43:37 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067904897  

Make a more complex test with a lot of ways to include pfr adapter.


📁 include/boost/fusion/support/config.hpp

```diff
 147 |+ #   define BOOST_FUSION_PFR_ENABLE_IMPLICIT_REFLECTION BOOST_PFR_ENABLED
 148 |+ #endif
 149 |+ 
```

> Username: denzor200  
> Created_at: 2023-01-12 08:43:15 UTC  
> Updated_at: 2023-01-12 09:49:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067843421  

Add static_assert here to reveal a strange configuration.  
  
Strange configuration - it's a configuration impossible to work, for example `BOOST_PFR_ENABLED==0 && BOOST_FUSION_PFR_ENABLE_IMPLICIT_REFLECTION==1`

---

📁 include/boost/fusion/support/config.hpp

```diff
 141 |+ 
 142 |+ #ifndef BOOST_FUSION_PFR_ENABLED
 143 |+ #   define BOOST_FUSION_PFR_ENABLED BOOST_PFR_ENABLE_IMPLICIT_REFLECTION
```

> Username: denzor200  
> Created_at: 2023-01-12 08:44:37 UTC  
> Updated_at: 2023-01-12 09:49:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067844770  

`#   define BOOST_FUSION_PFR_ENABLED BOOST_PFR_ENABLED`

> Username: denzor200  
> Created_at: 2023-01-27 09:33:51 UTC  
> Updated_at: 2023-01-27 09:33:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088750678  

Fixed

---

📁 include/boost/fusion/support/config.hpp

```diff
 145 |+ 
 146 |+ #ifndef BOOST_FUSION_PFR_ENABLE_IMPLICIT_REFLECTION
 147 |+ #   define BOOST_FUSION_PFR_ENABLE_IMPLICIT_REFLECTION BOOST_PFR_ENABLED
```

> Username: denzor200  
> Created_at: 2023-01-12 08:44:48 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067844924  

`#   define BOOST_FUSION_PFR_ENABLE_IMPLICIT_REFLECTION BOOST_PFR_ENABLE_IMPLICIT_REFLECTION`

> Username: denzor200  
> Created_at: 2023-01-27 09:34:05 UTC  
> Updated_at: 2023-01-27 09:34:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088750887  

Fixed


📁 include/boost/fusion/adapted/boost_pfr/tag_of_fallback.hpp

```diff
  28 |+         struct tag_of_fallback<T, std::enable_if_t<std::is_same<T, T>::value>>
  29 |+         {
  30 |+             static constexpr auto possible_pfr = boost::pfr::is_implicitly_reflectable<
```

> Username: denzor200  
> Created_at: 2023-01-12 09:20:47 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067881177  

Add static_assert here to check that `T` isn't incomplete.  
Otherwise just show a friendly message that suggests to disable implicit reflection.


📁 test/sequence/boost_pfr.cpp

```diff
   6 |+ 
   7 |+ #include <boost/detail/lightweight_test.hpp>
   8 |+ #include <boost/fusion/adapted/boost_pfr.hpp>
```

> Username: denzor200  
> Created_at: 2023-01-12 09:42:51 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067904081  

Make a more complex test with a lot of ways to include pfr adapter.  
For example, `boost/fusion/adapted/boost_pfr.hpp` here can be replaced to 'boost/fusion/adapted.hpp' , `boost/fusion/include/boost_pfr.hpp`, etc..


📁 test/sequence/boost_pfr_convert.cpp

```diff
   6 |+ 
   7 |+ #include <boost/detail/lightweight_test.hpp>
   8 |+ #include <boost/fusion/adapted/boost_pfr.hpp>
```

> Username: denzor200  
> Created_at: 2023-01-12 09:43:32 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067904806  

Make a more complex test with a lot of ways to include pfr adapter.


📁 test/sequence/boost_pfr_explicit.cpp

```diff
   6 |+ 
   7 |+ #include <boost/detail/lightweight_test.hpp>
   8 |+ #include <boost/fusion/adapted/boost_pfr_explicit.hpp>
```

> Username: denzor200  
> Created_at: 2023-01-12 09:43:43 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067905018  

Make a more complex test with a lot of ways to include pfr adapter.


📁 test/sequence/boost_pfr_force_macro.cpp

```diff
   6 |+ 
   7 |+ #include <boost/detail/lightweight_test.hpp>
   8 |+ #include <boost/fusion/adapted/boost_pfr_explicit.hpp>
```

> Username: denzor200  
> Created_at: 2023-01-12 09:43:50 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067905142  

Make a more complex test with a lot of ways to include pfr adapter.


📁 test/sequence/boost_pfr_iterator.cpp

```diff
   6 |+ 
   7 |+ #include <boost/detail/lightweight_test.hpp>
   8 |+ #include <boost/fusion/adapted/boost_pfr_explicit.hpp>
```

> Username: denzor200  
> Created_at: 2023-01-12 09:43:57 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067905285  

Make a more complex test with a lot of ways to include pfr adapter.


📁 include/boost/fusion/adapted.hpp

```diff
  26 |+ #if BOOST_FUSION_PFR_ENABLED
  27 |+ #if BOOST_FUSION_PFR_ENABLE_IMPLICIT_REFLECTION
  28 |+ #include <boost/fusion/adapted/boost_pfr.hpp>
```

> Username: denzor200  
> Created_at: 2023-01-12 09:46:53 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067908389  

Add a regression test for it. Maybe you just need to include `adapted.hpp` in some old tests?

> Username: denzor200  
> Created_at: 2023-01-27 09:36:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088752819  

Implemented regressiong tests. It's provided by `with_or_without_fallback.hpp` inclusion in every old test


📁 test/Jamfile

```diff
 276 |     [ run sequence/swap.cpp ]
 277 | 
 278 |+     [ run support/config.cpp ]
```

> Username: denzor200  
> Created_at: 2023-01-12 09:49:32 UTC  
> Updated_at: 2023-01-12 09:49:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1067911136  

Add `<test-info>always_show_run_output` here, like this: https://github.com/boostorg/pfr/blob/boost-1.81.0/test/Jamfile.v2#L52


---

## Review 2 [Commented]

> Username: denzor200  
> Created_at: 2023-01-26 15:42:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/266#pullrequestreview-1265044573  

📁 include/boost/fusion/adapted/boost_pfr/tag_of_fallback.hpp

```diff
  42 |+         // TODO: test for it
  43 |+         template <class T, std::size_t N>
  44 |+         constexpr bool in_namespace(const char (&ns)[N]) noexcept {
```

> Username: denzor200  
> Created_at: 2023-01-23 06:09:44 UTC  
> Updated_at: 2023-01-26 15:42:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1083675233  

Add tests for `detail::in_namespace` and `detail::starts_with`

---

📁 include/boost/fusion/adapted/boost_pfr/tag_of_fallback.hpp

```diff
  75 |+             const auto value = !std::is_array<T>::value
  76 |+                           // FIXME do we need this?
  77 |+                           // && !std::is_reference<T>::value
```

> Username: denzor200  
> Created_at: 2023-01-26 15:40:46 UTC  
> Updated_at: 2023-01-26 15:42:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088015127  

We needn't this. PFR doesn't work with references: https://godbolt.org/z/PEsWhf84c

> Username: denzor200  
> Created_at: 2023-01-27 09:39:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088755871  

> PFR doesn't work with references  
  
Maybe we should add a small "boost_pfr_reference" test to show it

> Username: denzor200  
> Created_at: 2023-01-29 09:27:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1089910482  

Added "boost_pfr_reference" test with a little bit limitations due to PFR side issue.


📁 test/sequence/comparison.hpp

```diff
  10 |+ #include <boost/fusion/adapted/boost_pfr/force.hpp>
  11 |+ 
  12 |+ // TODO: force.hpp should be documented and tested that it won't adapt pfr implicitly or explicitly
```

> Username: denzor200  
> Created_at: 2023-01-23 06:21:52 UTC  
> Updated_at: 2023-01-26 15:42:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1083680587  

-Remove `[caution This macro requires at least C++14! Pre C++14 compilers (C++11, C++03...) are not supported.]` for all 4 macroes.  
-Its "Header" section should look like:  
```  
    #include <boost/fusion/adapted/boost_pfr/force.hpp>  
    #include <boost/fusion/include/boost_pfr_force.hpp>  
```  
-Make a C++03 test, it will show that inclusion of `force.hpp` header and using of marcoes won't affect on `tag_of` implicitly or explicitly.


📁 include/boost/fusion/adapted/boost_pfr/force.hpp

```diff
  27 |+ #define BOOST_FUSION_FORCE_PFR_REFLECTABLE(NAME)                                               \
  28 |+     namespace boost { namespace pfr {                                                          \
  29 |+         template<> struct is_reflectable<NAME, boost::pfr::boost_fusion_tag>                   \
```

> Username: denzor200  
> Created_at: 2023-01-23 17:47:42 UTC  
> Updated_at: 2023-01-26 15:42:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1084364127  

Please, make a homogeneous format for all macroes.


📁 test/sequence/with_or_without_fallback.hpp

```diff
   1 |+ /*=============================================================================
```

> Username: denzor200  
> Created_at: 2023-01-25 14:22:45 UTC  
> Updated_at: 2023-01-26 15:42:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1086703074  

Should you make the one `with_or_without_fallback.hpp` and move it into `test/` directory?


📁 test/sequence/boost_pfr.cpp

```diff
  79 |+     using ns::point;
  80 |+ 
  81 |+     // FIXME make this workable even with forced implicit reflection
```

> Username: denzor200  
> Created_at: 2023-01-26 15:41:14 UTC  
> Updated_at: 2023-01-26 15:42:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088015720  

I suppose it will work now

> Username: denzor200  
> Created_at: 2023-01-29 09:26:10 UTC  
> Updated_at: 2023-01-29 09:26:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1089910298  

It's not works:( It isn't necessary part, I've just given it up


📁 test/sequence/boost_pfr_empty.cpp

```diff
  43 |+     namespace fusion = boost::fusion;
  44 |+ 
  45 |+     // FIXME make this workable even with forced implicit reflection
```

> Username: denzor200  
> Created_at: 2023-01-26 15:41:40 UTC  
> Updated_at: 2023-01-26 15:42:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088016318  

I suppose it will work now

> Username: denzor200  
> Created_at: 2023-01-29 09:26:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1089910284  

It's not works:( It isn't necessary part, I've just given it up


---

## Review 3 [Commented]

> Username: denzor200  
> Created_at: 2023-01-27 09:54:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/266#pullrequestreview-1272390524  

📁 test/include/_boost_pfr.cpp

```diff
   1 |+ /*=============================================================================
   2 |+     Copyright (c) 2021-2023 Denis Mikhailov
```

> Username: denzor200  
> Created_at: 2023-01-27 09:54:46 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088772103  

Do we really need these 3 new files in include directory?

> Username: denzor200  
> Created_at: 2023-01-27 13:50:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#discussion_r1088983260  

`adapted.cpp` is intended to show that there is no regression in depended on `adapted.hpp` old code. Now we have a better way for it.  
`_boost_pfr.cpp` and `_boost_pfr_explicit.cpp` are intended to show that their headers don't require to include something else. I suppose there is no sense for it.  
  
These 3 files have to be removed from this PR.


---

## Comment 4

> Username: denzor200  
> Created_at: 2023-01-29 10:57:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#issuecomment-1407629715  

@djowel, @daminetreg , @Kojoley I'm ready to show this PR.  
Would you like to join the review?

---

## Comment 5

> Username: denzor200  
> Created_at: 2023-03-18 16:42:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#issuecomment-1474905813  

@djowel Hello, are we going on?

---

## Comment 6

> Username: daminetreg  
> Created_at: 2023-03-20 08:39:21 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#issuecomment-1475821025  

Wow awesome that's terrific. I missed your comment from 29th January. I will do my best to review and try it out this Friday.

---

## Comment 7

> Username: denzor200  
> Created_at: 2023-03-20 08:45:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#issuecomment-1475830703  

@daminetreg Cool!

---

## Comment 8

> Username: denzor200  
> Created_at: 2023-04-25 05:38:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#issuecomment-1521178792  

@daminetreg Hi! I want to inform that I may be unavailable a month later for some time. Can we do something with this PR right now, or we will just postpone it for some time?  
  
How is your review going on, is there any trouble or just not enough time for it? Can I help you with something?  
  
Maybe I should reduce this PR throwing away the implicit reflection? We would merge the explicit one pretty fast, and then leisurely continue working on huge implicit reflection having already released the first PFR integration.

---

## Comment 9

> Username: daminetreg  
> Created_at: 2023-04-27 08:34:17 UTC  
> Updated_at: 2023-04-27 08:34:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#issuecomment-1525123563  

Hi @denzor200 , sorry for the delay I couldn't try yet but I will do my best to give my early feedback this week still. I think it's a very awesome feature, so we should not wait too long for merging. I apologies for my bandwidth here.

---

## Comment 10

> Username: xR3b0rn  
> Created_at: 2023-08-08 12:21:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#issuecomment-1669508605  

Any updates on this?

---

## Comment 11

> Username: denzor200  
> Created_at: 2023-08-19 09:29:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/266#issuecomment-1684903900  

@xR3b0rn everything ready from my side, waiting for review. As I informed above, I was unavailable for some months. But right now I'm available every day and can react to review pretty fast.  
  
@daminetreg

---
