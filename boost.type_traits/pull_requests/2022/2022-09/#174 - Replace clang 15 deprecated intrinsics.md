# #174 Replace clang 15 deprecated intrinsics [Merged]

> Username: ecatmur  
> Created at: 2022-09-06 19:06:13 UTC  
> Updated at: 2022-09-13 11:13:16 UTC  
> Merged at: 2022-09-13 11:08:26 UTC  
> Closed at: 2022-09-13 11:08:27 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174  

Fixes https://github.com/boostorg/type_traits/issues/173  
  
https://releases.llvm.org/15.0.0/tools/clang/docs/ReleaseNotes.html#non-comprehensive-list-of-changes-in-this-release  
  
> Some type-trait builtins, such as `__has_trivial_assign`, have been documented as deprecated for a while because their semantics don’t mix well with post-C++11 type-traits. Clang now emits deprecation warnings for them under the flag `-Wdeprecated-builtins`.  
  
Should be OK to replace with `__is_trivially_assignable` etc. (with some adjustments). Will post a patch.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2022-09-09 23:41:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/type_traits/pull/174#pullrequestreview-1103001383  

📁 include/boost/type_traits/intrinsics.hpp

```diff
 185 | #   endif
 186 |- #   if __has_feature(has_trivial_constructor)
 186 |+ #   if defined(__has_builtin) && __has_builtin(__is_trivially_constructible)
```

> Username: Lastique  
> Created_at: 2022-09-09 23:41:35 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174#discussion_r967542209  

This doesn't work if `__has_builtin` is not defined. The correct way to do this is:  
  
```  
#if defined(__has_builtin)  
#if __has_builtin(__is_trivially_constructible)  
...  
#endif  
#elif ...  
```  
  
The same everywhere else below.  
  
PS: I know clang defines `__has_builtin`, that's why it compiles. But since you're checking it, the code must work correctly if it is not defined too.

> Username: ecatmur  
> Created_at: 2022-09-11 17:51:35 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174#discussion_r967864533  

Hm, I thought this'd be OK since we're inside `#if defined(BOOST_CLANG) && ...`? It's what Boost.Config does in boost/config/compiler/clang.hpp. Happy to change it though.

> Username: ecatmur  
> Created_at: 2022-09-11 18:11:24 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174#discussion_r967867042  

```c++  
#if defined(__has_builtin)  
#if __has_builtin(__is_trivially_constructible)  
...  
#endif  
#elif ...  
```  
This won't work, since the `#elif` only kicks in if Clang doesn't have `__has_builtin`.  We'd need to repeat the branch or define a helper macro.  
  
Since we're inside `BOOST_CLANG` and every version of clang (since 3.0) has `__has_builtin`, maybe it'd be OK just to remove that unnecessary check?

> Username: Lastique  
> Created_at: 2022-09-11 23:10:40 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174#discussion_r967902163  

> Hm, I thought this'd be OK since we're inside `#if defined(BOOST_CLANG) && ...`?  
  
That condition is also checking for `__has_feature` being defined, so `BOOST_CLANG` is not taken as an indicator for its presence. So you shouldn't be assuming that `__has_builtin` is defined either.  
  
> It's what Boost.Config does in boost/config/compiler/clang.hpp. Happy to change it though.  
  
If you mean [this](https://github.com/boostorg/config/blob/8e94132dad65f171f06182e03eaf02c406552713/include/boost/config/compiler/clang.hpp#L330) then this is a bug in Boost.Config. It is fixed as part of https://github.com/boostorg/config/pull/442.  
  
> This won't work, since the `#elif` only kicks in if Clang doesn't have `__has_builtin`. We'd need to repeat the branch or define a helper macro.  
  
Yes, I wasn't suggesting to use exactly that preprocessor code, just illustrated how the conditions should be rearranged. You could move the check for `__has_builtin` to the `__has_feature` check above, for example, if that keeps the code correct and simple.  
  
> Since we're inside `BOOST_CLANG` and every version of clang (since 3.0) has `__has_builtin`, maybe it'd be OK just to remove that unnecessary check?  
  
Personally, I would not rely on that. Just do the check that you actually need instead of hoping it'll work.

> Username: ecatmur  
> Created_at: 2022-09-12 11:31:03 UTC  
> Updated_at: 2022-09-12 11:31:04 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174#discussion_r968301683  

> You could move the check for `__has_builtin` to the `__has_feature` check above, for example, if that keeps the code correct and simple.  
  
Yes, that looks like the right approach. Thanks!


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-09-12 16:38:29 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174#issuecomment-1244000637  

Looks like there were network issues - re-running the failed jobs now.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-09-12 17:54:42 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174#issuecomment-1244097215  

All looks good to me, is this all ready to merge now?

---

## Comment 4

> Username: Lastique  
> Created_at: 2022-09-13 10:02:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/174#issuecomment-1245183980  

Looks good to me.

---
