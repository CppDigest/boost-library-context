# #134 Constexpr conversion to MPL types [Open]

> Username: Kojoley  
> Created at: 2019-11-30 23:20:19 UTC  
> Updated at: 2020-08-14 14:30:06 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134  

Fixes #133  
  
GCC, Clang, MSVC, ICC, all of them are fine with this https://godbolt.org/z/6K6NDm

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2020-01-08 14:08:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/type_traits/pull/134#pullrequestreview-339885551  

📁 test/mpl_interop_test1.cpp

```diff
  11 | template <class T>
  10 |- int dispatch_test_imp(const boost::mpl::bool_<true>&)
  12 |+ BOOST_CONSTEXPR int dispatch_test_imp(boost::mpl::true_)
```

> Username: pdimov  
> Created_at: 2020-01-08 14:08:02 UTC  
> Updated_at: 2020-01-08 15:37:52 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#discussion_r364247826  

Why are you changing const ref to value here? Does the original no longer work?

> Username: Kojoley  
> Created_at: 2020-01-08 14:13:38 UTC  
> Updated_at: 2020-01-08 15:37:52 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#discussion_r364250346  

I do not actually remember already, most likely to show that it is copyable at compile time.

> Username: Kojoley  
> Created_at: 2020-01-08 14:20:35 UTC  
> Updated_at: 2020-01-08 15:37:52 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#discussion_r364253667  

Reverted it back.

> Username: pdimov  
> Created_at: 2020-01-08 15:23:25 UTC  
> Updated_at: 2020-01-08 15:37:52 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#discussion_r364286574  

Your modified test was valuable, just add it without removing the old one. Removing an old test implies that it no longer works as a result of a change, which isn't the case here.

> Username: Kojoley  
> Created_at: 2020-01-08 15:39:15 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#discussion_r364295275  

Agreed


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-01-08 18:36:42 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#issuecomment-572199630  

As noted in https://github.com/boostorg/type_traits/issues/133 I'm rather inclined to treat this as a non-bug, or at least one that can't be fixed without breaking stuff.  
  
The problem with this fix is it means that code that relies on this conversion, and on mpl::bool_ being at least forward declared after including a type_traits header will be broken (and will need to be modified to include the mpl headers explicitly, as you did for the test case, which quite deliberately did **not** do that).    
  
I realise they should be doing that already, but if we're forcing folks to change their code, it would be better to mark this interface as deprecated, and suggest they dispatch on integral_constant instead.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-01-08 18:40:01 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#issuecomment-572200959  

Ok, I just will create a new duplicate test that and will not touch this to show that nothing should be broken by this change.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2020-01-08 18:49:17 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#issuecomment-572204622  

Stop, why we should support the code that uses mpl types and not including mpl? Is not it a bug in their code?

---

## Comment 5

> Username: pdimov  
> Created_at: 2020-01-08 19:00:13 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#issuecomment-572209022  

It is, in my opinion, a bug in their code; and the answer to your question is probably "because code in unmaintained libraries will break and we'll need to fix it." :-)

---

## Comment 6

> Username: Kojoley  
> Created_at: 2020-01-08 19:08:39 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#issuecomment-572212243  

If the only concerns are about Boosts code, I volunteer myself to identify and fix them.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2020-01-09 12:03:20 UTC  
> Url: https://github.com/boostorg/type_traits/pull/134#issuecomment-572531939  

I made a list of libraries with `grep -ER "bool_\s*<\s*(true|false)\s*>" libs | cut -d / -f 2 | sort | uniq`, and run their tests with the PR applied https://travis-ci.com/Kojoley/type_traits_experiment/builds/143657865 and they are fine, except timeouted multiprecision and not related issues in hof/statechart.

---
