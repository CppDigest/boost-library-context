# #299 Tests: Improve tests [Merged]

> Username: Kojoley  
> Created at: 2017-11-28 00:11:52 UTC  
> Updated at: 2017-11-28 13:52:53 UTC  
> Merged at: 2017-11-28 00:15:25 UTC  
> Closed at: 2017-11-28 00:15:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/299  

- Split Spirit v2 tests  
  - Readd missing v2 tests  
  - Run X3 tests  
  - Remove `-p3` suffix  
  - Use `<c++-template-depth>`  
  - Automatic adding of subproject name prefix  
  - Flatten tests declaration

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-11-28 00:15:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#issuecomment-347371747  

It is now should be safe to merge as https://github.com/boostorg/build/commit/6cea15c958ba3a5a60d6de40236a1f2cdbde20ae landed.  
At least `b2 status/ -n -a -d0 --dump-tests --reconfigure` now correctly shows all the Spirit tests.

---

## Review 2 [Commented]

> Username: octopus-prime  
> Created_at: 2017-11-28 11:39:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/299#pullrequestreview-79474384  

📁 test/x3/Jamfile

```diff
  79 |+ run optional.cpp ;
  80 |+ run plus.cpp ;
  81 |+ run with.cpp ;
```

> Username: octopus-prime  
> Created_at: 2017-11-28 11:39:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#discussion_r153466982  

Where is container_support.cpp ??

> Username: Kojoley  
> Created_at: 2017-11-28 12:19:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#discussion_r153475070  

There are some other missed tests. I did not catch up all of them, it is a part of the other PR.

> Username: octopus-prime  
> Created_at: 2017-11-28 13:52:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#discussion_r153496731  

Which other PR?  
Is there a ticket for adding kicked tests again - containing a list of kicked test to add later again?  
  
Big chance to forget/miss some of the kicked tests...


---

## Review 3 [Commented]

> Username: octopus-prime  
> Created_at: 2017-11-28 11:40:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/299#pullrequestreview-79474514  

📁 test/x3/Jamfile

```diff
  95 |+ run symbols2.cpp ;
  96 |+ run symbols3.cpp ;
  97 |+ run tst.cpp /boost//system ;
```

> Username: octopus-prime  
> Created_at: 2017-11-28 11:40:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#discussion_r153467078  

Why there is still useless dependency to boost-system??


---

## Review 4 [Commented]

> Username: octopus-prime  
> Created_at: 2017-11-28 11:41:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/299#pullrequestreview-79474751  

📁 test/x3/Jamfile

```diff
  26 |-     :
  27 |-     :
  24 |     ;
```

> Username: octopus-prime  
> Created_at: 2017-11-28 11:41:12 UTC  
> Updated_at: 2017-11-28 11:41:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#discussion_r153467257  

Why there is `-std=c++1y`? The c++ dialect is injected from outside into the test, isn't it?

> Username: Kojoley  
> Created_at: 2017-11-28 12:19:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#discussion_r153475238  

It is requirements, but actually they seems not to work as expected. I did not wrote it, but fixing this was not a plan for this PR.


---

## Review 5 [Commented]

> Username: octopus-prime  
> Created_at: 2017-11-28 11:42:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/299#pullrequestreview-79474992  

📁 test/qi/Jamfile

```diff
 115 |+ run symbols2.cpp ;
 116 |+ run terminal_ex.cpp ;
 117 |+ run tst.cpp /boost//system ;
```

> Username: octopus-prime  
> Created_at: 2017-11-28 11:42:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#discussion_r153467443  

Why there is still useless dependency to boost-system?

> Username: Kojoley  
> Created_at: 2017-11-28 12:17:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/299#discussion_r153474825  

I know at least one toolset it requires - MinGW.


---
