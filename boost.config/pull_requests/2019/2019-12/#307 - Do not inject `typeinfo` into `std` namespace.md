# #307 Do not inject `typeinfo` into `std` namespace [Open]

> Username: Kojoley  
> Created at: 2019-12-02 18:33:31 UTC  
> Updated at: 2020-07-25 18:56:46 UTC  
> Url: https://github.com/boostorg/config/pull/307  

`BOOST_NO_STD_TYPEINFO` should be used to detect if there is no `typeinfo` in `std` namespace, or Boost.Core/Boost.TypeInfo wrappers for a portable solution.  
  
Closes #306.

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-07-08 02:20:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/307#pullrequestreview-444368209  

📁 include/boost/config/detail/suffix.hpp

```diff
 534 |- #include <typeinfo>
 535 |- namespace std{ using ::type_info; }
 536 |- #endif
```

> Username: jeremy-murphy  
> Created_at: 2020-07-08 02:20:36 UTC  
> Url: https://github.com/boostorg/config/pull/307#discussion_r451244252  

I have to manually delete this block of code to get Boost.Config to compile with the IAR compiler, so I hope this PR gets merged soon!

> Username: jeremy-murphy  
> Created_at: 2020-07-08 03:13:36 UTC  
> Url: https://github.com/boostorg/config/pull/307#discussion_r451257513  

As I mentioned on the issue tracker, I'm not sure removing these lines is the best solution after all.

> Username: jzmaddock  
> Created_at: 2020-07-08 07:18:03 UTC  
> Updated_at: 2020-07-08 07:18:04 UTC  
> Url: https://github.com/boostorg/config/pull/307#discussion_r451332515  

This was an MSVC-specific workaround as I recall, if I restrict the code to MSVC does that help?  
  
BTW is there really no typeinfo?  What else is missing?

> Username: jeremy-murphy  
> Created_at: 2020-07-09 04:16:11 UTC  
> Url: https://github.com/boostorg/config/pull/307#discussion_r451954024  

Really and truly. Granted, this is _because_ I have disabled exceptions and RTTI on this compiler because our target is embedded. So for example I have `BOOST_NO_EXCEPTIONS`, `BOOST_NO_RTTI` and `BOOST_NO_TYPEID` defined in the preprocessor, and that solves most anticipated problems.


---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2020-07-24 05:50:22 UTC  
> Url: https://github.com/boostorg/config/pull/307#issuecomment-663358250  

@Kojoley I think you need to resolve the merge conflicts for this to ever have a hope of being merged.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-07-24 14:44:51 UTC  
> Url: https://github.com/boostorg/config/pull/307#issuecomment-663574487  

The change it conflicts with (035c096da942a3fa7f75180ae8b2f2732db0a9f4) is just wrong and should be reverted, then this will land cleanly.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-07-25 18:49:04 UTC  
> Url: https://github.com/boostorg/config/pull/307#issuecomment-663888178  

The problem I have is that this PR breaks the original purpose of those workarounds.  
  
Try this: https://github.com/boostorg/config/commit/46e0a134966bebb5496a2fd8d26a101e4b94ca95

---

## Comment 5

> Username: Kojoley  
> Created_at: 2020-07-25 18:56:16 UTC  
> Updated_at: 2020-07-25 18:56:46 UTC  
> Url: https://github.com/boostorg/config/pull/307#issuecomment-663888917  

What is `the original purpose of those workarounds`? To slow compilation for everyone because of Boost.LexicalCast? I fixed Boost.LexicalCast and it is not need the workaround in Boost.Config.

---
