# #495 add LTO support [Merged]

> Username: grisumbras  
> Created at: 2019-10-24 13:31:07 UTC  
> Updated at: 2021-10-02 21:13:47 UTC  
> Merged at: 2019-10-28 12:18:51 UTC  
> Closed at: 2019-10-28 12:18:51 UTC  
> Url: https://github.com/boostorg/build/pull/495  

Adds lto feature with 2 values: off (the default) and on. The feature  
enables link-time optimizations. Also adds support for the feature to  
gcc, clang and msvc toolsets.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2019-10-24 14:19:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/495#pullrequestreview-306609337  

📁 src/tools/clang-darwin.jam

```diff
  93 |+ # LTO
  94 |+ toolset.flags clang-darwin.compile OPTIONS <lto>on : -flto=thin ;
  95 |+ toolset.flags clang-darwin.link OPTIONS <lto>on : -flto=thin ;
```

> Username: grafikrobot  
> Created_at: 2019-10-24 14:16:24 UTC  
> Updated_at: 2019-10-27 07:27:01 UTC  
> Url: https://github.com/boostorg/build/pull/495#discussion_r338599489  

Why use thin instead of using the compiler default? Either we leave it up to the compiler, or we should add a second feature to control the kind of LTO to use.

> Username: grisumbras  
> Created_at: 2019-10-25 11:12:44 UTC  
> Updated_at: 2019-10-27 07:27:01 UTC  
> Url: https://github.com/boostorg/build/pull/495#discussion_r339001537  

I added lto-mode subfeature that allows to choose between thin and full. Also, made `-flto` the default.


📁 src/tools/features/lto-feature.jam

```diff
   4 |+ # http://www.boost.org/LICENSE_1_0.txt)
   5 |+ 
   6 |+ # TODO: Documentation.
```

> Username: grafikrobot  
> Created_at: 2019-10-24 14:18:19 UTC  
> Updated_at: 2019-10-27 07:27:01 UTC  
> Url: https://github.com/boostorg/build/pull/495#discussion_r338600642  

Definitely need some docs here ;-)

> Username: grisumbras  
> Created_at: 2019-10-25 11:13:10 UTC  
> Updated_at: 2019-10-27 07:27:01 UTC  
> Url: https://github.com/boostorg/build/pull/495#discussion_r339001651  

Added docs.


---

## Comment 2

> Username: grisumbras  
> Created_at: 2019-10-27 07:28:21 UTC  
> Url: https://github.com/boostorg/build/pull/495#issuecomment-546668815  

Ok, now I think the feature is ready.

---
