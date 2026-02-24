# #890 - Explore C++14 constexpr for url_view [Closed]

> Username: alandefreitas  
> Created at: 2024-12-13 17:27:52 UTC  
> Updated at: 2026-02-20 02:42:25 UTC  
> Closed at: 2026-02-20 02:42:25 UTC  
> Url: https://github.com/boostorg/url/issues/890  

Investigate what's stopping a boost::urls::url_view from being `constexpr`.  
  
We might be able to make it conditionally `constexpr` only under c++14. The obstacle might be that parsing is heavily buffer-oriented.  
  
See: [BOOST_CXX14_CONSTEXPR and BOOST_CONSTEXPR](https://www.boost.org/doc/libs/latest/libs/config/doc/html/boost_config/boost_macro_reference.html).  
  
<!-- https://cpplang.slack.com/archives/C01JR6C9C4U/p1734109482027369 -->

---

## Comment 1

> Username: alandefreitas  
> Created at: 2026-01-29 18:09:04 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3819354061  

The parse functions in can’t be constexpr in C++14 because they return system::result<T>. system::result<T> is a literal type only in C++20.

---

## Comment 2

> Username: pdimov  
> Created at: 2026-01-29 18:23:33 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3819426123  

That's not true; `system::result<T>` is literal in C++14 when `T` is literal. See https://godbolt.org/z/ThK9PWzfE.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2026-01-29 18:52:19 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3819648535  

https://godbolt.org/z/ThK9PWzfE

---

## Comment 4

> Username: alandefreitas  
> Created at: 2026-02-03 22:02:39 UTC  
> Updated at: 2026-02-04 00:51:54 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3843913325  

I implemented the feature in https://github.com/alandefreitas/url/tree/fix-890.   
  
The commit ended up with ~5,034 lines changed, including some small breaking changes. We had to:  
  
- Move parsing algorithms to header files  
- Create a `grammar::detail::constexpr_result<T>` which stores `grammar::error` enum values directly, deferring `error_code` creation until conversion to `system::result` to bypass the non-constexpr virtual function call in `error_category::failed()`.  
- Add this `constexpr_result` type with C++23+ source location support. C++14 up to C++20 would lose source location support because the macro creates a static constexpr variable with the source location.  
- We created alternative parsing algorithms to bypass `boost::optional` and `boost::variant2` of `boost::optional` when they could but were not literal.  
- Refactor grammar rules to return `constexpr_result` internally  
- Replace `pi_` with `external_impl_` in `url_view_base` and use `nullptr` as the sentinel to eliminate the self-referencing pointer that required `static` for constexpr.  
- Add `char const*` overloads using `detail::strlen` for C++14  
- Add constexpr tests in `tests/url_view.cpp`  
  
Considering how large this change has become and how few people have actually requested it, I'll mark this as unactionable for now and focus on the security report instead.

---

## Comment 5

> Username: pdimov  
> Created at: 2026-02-04 00:27:16 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3844555247  

> We created alternative parsing algorithms to bypass boost::optional and boost::variant2 when they could but were not literal.  
  
In what scenario boost::variant2 can be, but isn't, literal?

---

## Comment 6

> Username: alandefreitas  
> Created at: 2026-02-04 00:35:56 UTC  
> Updated at: 2026-02-04 00:37:15 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3844578936  

> In what scenario boost::variant2 can be, but isn't, literal?  
  
For instance, when it contains non-literal types, like boost::optional. I was being descriptive. Not attributing blame. 😅

---

## Comment 7

> Username: pdimov  
> Created at: 2026-02-04 00:39:46 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3844589257  

I don't understand how a type that contains non-literal types is supposed to be literal.

---

## Comment 8

> Username: alandefreitas  
> Created at: 2026-02-04 00:49:45 UTC  
> Updated at: 2026-02-04 00:50:32 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3844617613  

> I don't understand how a type that contains non-literal types is supposed to be literal.  
  
It's not. Only boost::optional can change this. I was being descriptive. Not attributing blame. 🙂

---

## Comment 9

> Username: alandefreitas  
> Created at: 2026-02-08 20:07:09 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3868153507  

> That's not true; system::result<T> is literal in C++14 when T is literal. See https://godbolt.org/z/ThK9PWzfE.  
  
Is there a way to make it work with a custom error code?  
  
https://godbolt.org/z/38nTdvzEj

---

## Comment 10

> Username: pdimov  
> Created at: 2026-02-09 03:16:57 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3869061934  

I can fix C++20 and later, but before that, constexpr virtuals aren't going to work.

---

## Comment 11

> Username: alandefreitas  
> Created at: 2026-02-09 05:32:10 UTC  
> Url: https://github.com/boostorg/url/issues/890#issuecomment-3869433268  

Nice. That would be great. 🚀   
  
I quickly experimented with a solution that used `boost::system::result<url_view, boost::urls::error>` internally (and the final conversion step could be different at runtime vs. compile time), but it also has its own problems.   
  
I think supporting C++20 is a very reasonable and justifiable solution for now, and then we can revisit C++17/C++14 if enough people "clamour" (😱) for that. 🙂
