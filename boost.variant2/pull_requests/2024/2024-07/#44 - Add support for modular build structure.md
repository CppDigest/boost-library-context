# #44 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:34:18 UTC  
> Updated at: 2024-08-28 18:43:26 UTC  
> Merged at: 2024-08-28 18:43:25 UTC  
> Closed at: 2024-08-28 18:43:25 UTC  
> Url: https://github.com/boostorg/variant2/pull/44  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-07-21 09:46:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/variant2/pull/44#pullrequestreview-2190287303  

📁 test/Jamfile

```diff
  17 | 
  18 |   : requirements
  19 |+     <source>/boost/config//boost_config
```

> Username: pdimov  
> Created_at: 2024-07-21 09:46:18 UTC  
> Updated_at: 2024-07-21 09:46:19 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685697116  

Why are these `<source>` requirements instead of `<library>` requirements?

> Username: grafikrobot  
> Created_at: 2024-07-21 13:30:23 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685740508  

I forgot the step to replace those before creating the PRs. I have those all changed locally. Just waiting to do more fixes to push.


---

## Comment 2

> Username: pdimov  
> Created_at: 2024-07-21 09:49:07 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#issuecomment-2241548145  

Are these dependencies actually required? I see nothing in the PR that depends on them.  
  
Conversely, doesn't it depend on the PRs for the library dependencies being merged first? Where does e.g. `/boost/mp11` come from? Although CI seems to have no problem with it for some reason.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-07-21 13:34:29 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#issuecomment-2241613677  

> Are these dependencies actually required? I see nothing in the PR that depends on them.  
  
According to https://pdimov.github.io/boostdep-report/develop/variant2.html yes.  
  
> Conversely, doesn't it depend on the PRs for the library dependencies being merged first? Where does e.g. `/boost/mp11` come from? Although CI seems to have no problem with it for some reason.  
  
Those get created by the boost-root/jamroot as needed until the transition is fully complete. Although I missed some cases that I'll need to fix. But that was the point of creating all the PRs. To shake out all the issues.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-07-21 18:28:18 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#issuecomment-2241735409  

> According to https://pdimov.github.io/boostdep-report/develop/variant2.html yes.  
  
I meant the PR dependencies listed above:  
  
https://github.com/boostorg/boost/pull/854  
https://github.com/boostorg/auto_index/pull/7  
https://github.com/boostorg/bcp/pull/20  
https://github.com/boostorg/boost_install/pull/69  
https://github.com/boostorg/boost/pull/890  
https://github.com/boostorg/boostbook/pull/25  
https://github.com/boostorg/boostdep/pull/21  
https://github.com/boostorg/docca/pull/143  
https://github.com/boostorg/inspect/pull/19  
https://github.com/boostorg/quickbook/pull/25  
  
Why would this PR depend on the tools? It seems to work as-is.

---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2024-07-21 18:32:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/variant2/pull/44#pullrequestreview-2190381200  

📁 build.jam

```diff
  16 |+ explicit
  17 |+     [ alias boost_variant2 ]
  18 |+     [ alias all : boost_variant2 test ]
```

> Username: pdimov  
> Created_at: 2024-07-21 18:32:16 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685791500  

Do we need the `all` target? Why?

> Username: grafikrobot  
> Created_at: 2024-07-21 18:35:19 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685791899  

It helps in my automation scripts to be able to check everything works.

> Username: pdimov  
> Created_at: 2024-07-21 18:37:21 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685792119  

As a general rule, `test` already depends on the library, so there's no need for `all`. If it doesn't, it's not much of a test.

> Username: grafikrobot  
> Created_at: 2024-07-21 18:41:24 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685792815  

Some libraries also have examples, extra tests, and performance checks. So it's not just the test target.


---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2024-07-21 18:32:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/variant2/pull/44#pullrequestreview-2190381263  

📁 build.jam

```diff
   7 |+ 
   8 |+ project /boost/variant2
   9 |+     : common-requirements
```

> Username: pdimov  
> Created_at: 2024-07-21 18:32:44 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685791553  

Why are these project requirements and not requirements of `boost_variant2`?

> Username: grafikrobot  
> Created_at: 2024-07-21 18:37:01 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685792070  

Some libs have multiple targets. And having them as project requirements makes them apply to all sub-projects. Which is what most, if not all libraries, already assume.

> Username: pdimov  
> Created_at: 2024-07-21 18:48:28 UTC  
> Updated_at: 2024-07-21 18:48:29 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685793808  

I most certainly don't want these to apply to all subprojects, e.g. `doc`. They have nothing to do with the subprojects.

> Username: grafikrobot  
> Created_at: 2024-07-22 01:00:36 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#discussion_r1685855162  

I can see your point. I would have to move those to a target. And have non-doc subprojects use that target. It's a bunch more work. :-(


---

## Comment 7

> Username: grafikrobot  
> Created_at: 2024-07-21 18:33:13 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#issuecomment-2241736568  

> Why would this PR depend on the tools? It seems to work as-is.  
  
It may well not depend on them. But I can't guarantee that building docs will work for any particular library without the tools first. As most libraries do not check if their docs build in CI.

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-07-21 18:35:32 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#issuecomment-2241737080  

I don't think that `build.jam` should affect the docs, regardless of whether tools are updated.  
  
(It currently does, but it shouldn't.)

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2024-08-18 16:06:15 UTC  
> Url: https://github.com/boostorg/variant2/pull/44#issuecomment-2295312046  

Please review and merge this PR at your earliest convenience.

---
