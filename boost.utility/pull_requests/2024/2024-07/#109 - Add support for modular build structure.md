# #109 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:26:08 UTC  
> Updated at: 2025-04-15 13:45:33 UTC  
> Merged at: 2025-04-13 14:39:40 UTC  
> Closed at: 2025-04-13 14:39:40 UTC  
> Url: https://github.com/boostorg/utility/pull/109  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
- https://github.com/boostorg/type_traits/pull/195  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 23:24:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/109#pullrequestreview-2190077291  

📁 test/Jamfile.v2

```diff
  33 | compile string_view_constexpr_test1.cpp ;
  34 |- run string_view_test1.cpp ;
  34 |+ run string_view_test1.cpp : : : <source>/boost/container_hash//boost_container_hash ;
```

> Username: Lastique  
> Created_at: 2024-07-20 23:21:04 UTC  
> Updated_at: 2024-07-20 23:24:44 UTC  
> Url: https://github.com/boostorg/utility/pull/109#discussion_r1685550234  

Why are these `<source>` dependencies rather than normal "linking" dependencies?


📁 test/call_traits_test.cpp

```diff
  21 |+ // type_traits/test utilities
  22 |+ #include <test.hpp>
  23 |+ #include <check_type.hpp>
```

> Username: Lastique  
> Created_at: 2024-07-20 23:24:15 UTC  
> Updated_at: 2024-07-20 23:24:44 UTC  
> Url: https://github.com/boostorg/utility/pull/109#discussion_r1685550461  

This looks like it has more potential for including a wrong header, if one of these headers are present in the include paths, other than from `type_traits/test`. I think, this needs to be handled better.

> Username: grafikrobot  
> Created_at: 2024-07-25 05:02:26 UTC  
> Url: https://github.com/boostorg/utility/pull/109#discussion_r1690818878  

Good point. Using `<test/test.hpp>` is not much better. Including a different library's internal test source is not the best thing to do. Will have to think about this one.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-07-28 16:57:23 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2254580070  

An automated preview of the documentation is available at [https://109.utility.prtest2.cppalliance.org/libs/utility/index.html](https://109.utility.prtest2.cppalliance.org/libs/utility/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-08-11 02:06:29 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2282352322  

An automated preview of the documentation is available at [https://109.utility.prtest2.cppalliance.org/libs/utility/index.html](https://109.utility.prtest2.cppalliance.org/libs/utility/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-08-17 06:32:27 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2294690904  

An automated preview of the documentation is available at [https://109.utility.prtest2.cppalliance.org/libs/utility/index.html](https://109.utility.prtest2.cppalliance.org/libs/utility/index.html)

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2024-08-21 04:01:07 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2300704540  

Resetting to retest for merged dependencies.

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2024-08-21 04:09:23 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2300747673  

Please review and merge this PR at your earliest convenience.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2025-04-13 13:18:21 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2799950574  

@Lastique tests look good for this. Can it get merged please?

---

## Comment 8

> Username: Lastique  
> Created_at: 2025-04-13 14:39:47 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2799979130  

Thanks.

---

## Comment 9

> Username: Lastique  
> Created_at: 2025-04-15 10:59:28 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2804663726  

@grafikrobot This broke GitHub Actions. See e.g. [here](https://github.com/boostorg/utility/actions/runs/14433146373/job/40470375517) and [here](https://github.com/boostorg/utility/actions/runs/14433146373/job/40470374586). Note that depinst did checkout type_traits, but it still isn't found.

---

## Comment 10

> Username: grafikrobot  
> Created_at: 2025-04-15 13:45:32 UTC  
> Url: https://github.com/boostorg/utility/pull/109#issuecomment-2805113617  

@Lastique sorry about the breakage. Had forgotten about that sequencing dependency.

---
