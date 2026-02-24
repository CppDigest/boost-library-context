# #390 Add how-to clone the superproject to README.md [Merged]

> Username: mloskot  
> Created at: 2020-04-17 15:44:18 UTC  
> Updated at: 2020-04-18 20:06:29 UTC  
> Merged at: 2020-04-17 17:49:44 UTC  
> Closed at: 2020-04-17 17:49:44 UTC  
> Url: https://github.com/boostorg/boost/pull/390  

Simplest way to address these issues https://lists.boost.org/Archives/boost/2020/04/248731.php  
  
If one aims to clone a repo from GitHub, then README.md is the obvious muscle-memorised place to look at.  
  
-----  
  
By the way, a curated `CONTRIBUTING.md` could replace the mess on the Wiki as a single source of truth.

---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2020-04-17 15:47:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/390#pullrequestreview-395569585  

📁 README.md

```diff
  28 |+ 
  29 |+ ```console
  30 |+ git clone --recurse-submodules https://github.com/boostorg/boost.git boost-root
```

> Username: Flamefire  
> Created_at: 2020-04-17 15:47:43 UTC  
> Updated_at: 2020-04-17 17:11:56 UTC  
> Url: https://github.com/boostorg/boost/pull/390#discussion_r410310281  

```diff  
-git clone --recurse-submodules https://github.com/boostorg/boost.git boost-root  
+git clone https://github.com/boostorg/boost.git boost-root  
```

> Username: mloskot  
> Created_at: 2020-04-17 15:50:17 UTC  
> Updated_at: 2020-04-17 17:11:56 UTC  
> Url: https://github.com/boostorg/boost/pull/390#discussion_r410311960  

Ha, amazing! Thanks very much


---

## Review 2 [Commented]

> Username: Flamefire  
> Created_at: 2020-04-17 15:47:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/390#pullrequestreview-395569745  

📁 README.md

```diff
  37 |+ 
  38 |+ ```console
  39 |+ git clone --recurse-submodules https://github.com/boostorg/boost.git boost-root
```

> Username: Flamefire  
> Created_at: 2020-04-17 15:47:55 UTC  
> Updated_at: 2020-04-17 17:11:56 UTC  
> Url: https://github.com/boostorg/boost/pull/390#discussion_r410310423  

```diff  
-git clone --recurse-submodules https://github.com/boostorg/boost.git boost-root  
+git clone https://github.com/boostorg/boost.git boost-root  
```

> Username: mloskot  
> Created_at: 2020-04-17 15:50:28 UTC  
> Updated_at: 2020-04-17 17:11:56 UTC  
> Url: https://github.com/boostorg/boost/pull/390#discussion_r410312067  

Fixed too


---

## Review 3 [Commented]

> Username: Kojoley  
> Created_at: 2020-04-18 15:05:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/390#pullrequestreview-395921590  

📁 README.md

```diff
  30 |+ git clone https://github.com/boostorg/boost.git boost-root
  31 |+ cd boost-root
  32 |+ git submodule update --init tools/boostdep
```

> Username: Kojoley  
> Created_at: 2020-04-18 15:05:24 UTC  
> Updated_at: 2020-04-18 15:05:25 UTC  
> Url: https://github.com/boostorg/boost/pull/390#discussion_r410709326  

one-liner `git clone --recurse-submodules="tools/boostdep" https://github.com/boostorg/boost.git boost-root`

> Username: mloskot  
> Created_at: 2020-04-18 20:03:50 UTC  
> Url: https://github.com/boostorg/boost/pull/390#discussion_r410744057  

The aim was not to show off a clever git fu but present self-explanatory examples that even those who just know git basics can understand yhe differences between 2-3 possible or common workflows.


---

## Comment 4

> Username: glenfe  
> Created_at: 2020-04-18 15:55:51 UTC  
> Url: https://github.com/boostorg/boost/pull/390#issuecomment-615893965  

Maybe we can link from the README.md to a page on boostorg/wiki and you can edit that page more regularly.

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-04-18 20:06:29 UTC  
> Url: https://github.com/boostorg/boost/pull/390#issuecomment-615937122  

I'd be against wiki. Wikis either rot or drift away. PR driven updates ensure a second pair of eyes do review before merge.

---
