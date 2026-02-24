# #2027 Fix file open with append_existing flag on posix [Closed]

> Username: madmongo1  
> Created at: 2020-07-21 15:40:45 UTC  
> Updated at: 2020-08-29 09:39:51 UTC  
> Closed at: 2020-08-29 09:39:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2027  

fixes #2011

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-07-21 15:41:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2027#issuecomment-661937874  

@vinniefalco pls check that this was the intent.

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-21 15:53:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2027#pullrequestreview-452599154  

📁 test/beast/core/CMakeLists.txt

```diff
  80 |+         lib-beast
  81 |+         lib-test
  82 |+         )
```

> Username: vinniefalco  
> Created_at: 2020-07-21 15:53:14 UTC  
> Updated_at: 2020-07-21 16:00:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2027#discussion_r458205685  

missing newline


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-21 15:54:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2027#pullrequestreview-452600295  

📁 test/beast/core/CMakeLists.txt

```diff
  74 | set_property(TARGET tests-beast-core PROPERTY FOLDER "tests")
  75 |+ 
  76 |+ add_executable(_2011 2011.cpp)
```

> Username: vinniefalco  
> Created_at: 2020-07-21 15:54:28 UTC  
> Updated_at: 2020-07-21 16:00:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2027#discussion_r458206565  

umm... we're adding a whole executable just to test this issue?


---

## Comment 4

> Username: madmongo1  
> Created_at: 2020-07-21 15:57:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2027#issuecomment-661947376  

Dammit, how did that get in there?  
  
  
On Tue, 21 Jul 2020 at 17:54, Vinnie Falco <notifications@github.com> wrote:  
  
> *@vinniefalco* commented on this pull request.  
> ------------------------------  
>  
> In test/beast/core/CMakeLists.txt  
> <https://github.com/boostorg/beast/pull/2027#discussion_r458206565>:  
>  
> > @@ -72,3 +72,11 @@ target_link_libraries(tests-beast-core  
>      )  
>  
>  set_property(TARGET tests-beast-core PROPERTY FOLDER "tests")  
> +  
> +add_executable(_2011 2011.cpp)  
>  
> umm... we're adding a whole executable just to test this issue?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/2027#pullrequestreview-452600295>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSLGSWXCL27XQFCEVH3R4W24TANCNFSM4PDXKTRQ>  
> .  
>  
  
  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2020-07-21 17:51:16 UTC  
> Updated_at: 2020-07-21 18:35:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2027#issuecomment-662012332  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2027?src=pr&el=h1) Report  
> Merging [#2027](https://codecov.io/gh/boostorg/beast/pull/2027?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3486e9cb18aa39b392e07031a33e65b1792fbccf&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2027/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2027?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2027      +/-   ##  
===========================================  
- Coverage    95.09%   95.08%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11939    11939                
===========================================  
- Hits         11353    11352       -1       
- Misses         586      587       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2027?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/file\_posix.ipp](https://codecov.io/gh/boostorg/beast/pull/2027/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmlsZV9wb3NpeC5pcHA=) | `83.21% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2027/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2027?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2027?src=pr&el=footer). Last update [3486e9c...17adcbf](https://codecov.io/gh/boostorg/beast/pull/2027?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 6

> Username: stale[bot]  
> Created_at: 2020-08-22 19:00:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2027#issuecomment-678678904  

This issue has been open for a while with no activity, has it been resolved?

---
