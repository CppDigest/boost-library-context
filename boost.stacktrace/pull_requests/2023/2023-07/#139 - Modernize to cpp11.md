# #139 Modernize to cpp11 [Merged]

> Username: leha-bot  
> Created at: 2023-07-20 13:23:11 UTC  
> Updated at: 2023-08-12 07:46:32 UTC  
> Merged at: 2023-08-11 18:32:41 UTC  
> Closed at: 2023-08-11 18:32:41 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/139  

- [x] Drop `pragma message` in favour of `#error`;  
 - [x] Use noexcept instead of `BOOST_NOEXCEPT`;  
 - [x] Use `<type_traits>` instead of Boost.TypeTraits;  
 - [x] Use `<array>` instead of Boost.Array;  
 - [x] Use `static_assert` instead of Boost.StaticAssert;  
 - [x] Use `constexpr` instead of `BOOST_CONSTEXPR`;  
 - [x] Fix CI;  
 - [ ] ...

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2023-07-21 06:15:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/stacktrace/pull/139#pullrequestreview-1540514733  

📁 include/boost/stacktrace/detail/frame_decl.hpp

```diff
 142 |     /// @cond
 145 |-     BOOST_CONSTEXPR bool operator!() const BOOST_NOEXCEPT { return !address(); }
 143 |+     constexpr bool operator!() const noexcept { return !address(); }
```

> Username: apolukhin  
> Created_at: 2023-07-21 06:12:32 UTC  
> Updated_at: 2023-07-21 06:15:09 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/139#discussion_r1270262776  

Remove the whole function

> Username: leha-bot  
> Created_at: 2023-07-21 08:00:46 UTC  
> Updated_at: 2023-07-21 08:00:47 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/139#discussion_r1270356798  

done (in two places)


📁 example/assert_handler.cpp

```diff
  17 | BOOST_NOINLINE void bar(int i) {
  15 |-     boost::array<int, 5> a = {{101, 100, 123, 23, 32}};
  18 |+     std::array<int, 5> a = {{101, 100, 123, 23, 32}};
```

> Username: apolukhin  
> Created_at: 2023-07-21 06:13:26 UTC  
> Updated_at: 2023-07-21 06:41:38 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/139#discussion_r1270263281  

Here, and in the following tests, leave the `std::array`. Test shows how to configure BOOST_ASSERT to use stacktraces

> Username: leha-bot  
> Created_at: 2023-07-21 08:01:01 UTC  
> Updated_at: 2023-07-21 08:01:02 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/139#discussion_r1270357026  

done, reverted, thanks


---

## Comment 2

> Username: coveralls  
> Created_at: 2023-07-21 14:52:37 UTC  
> Updated_at: 2023-08-10 15:46:31 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/139#issuecomment-1645716394  

[![Coverage Status](https://coveralls.io/builds/61911560/badge)](https://coveralls.io/builds/61911560)  
  
coverage: 92.51% (+1.6%) from 90.927% when pulling **fec3aa1e4ad4a2664a122f42516025beec570dd2 on leha-bot:modernize-to-cpp11** into **c6e771286801ca7f5f58b3d6a812b95b85e71bc0 on boostorg:develop**.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2023-08-12 07:46:31 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/139#issuecomment-1675771155  

Many thanks for the PR!

---
