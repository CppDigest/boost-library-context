# #23 Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2018-12-30 14:29:32 UTC  
> Updated at: 2019-01-05 07:25:14 UTC  
> Merged at: 2018-12-31 16:08:04 UTC  
> Closed at: 2018-12-31 16:08:04 UTC  
> Url: https://github.com/boostorg/move/pull/23  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
     add_subdirectory( <path-to-boost_move> )  
     target_link_library(<my_lib> PUBLIC Boost::move)  
  
More information:   
- https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M%5B1-25%5D  
- https://groups.google.com/d/msg/boost-developers-archive/4HU-RzReL7U/FS1X6OFrEQAJ  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-12-30 15:25:15 UTC  
> Url: https://github.com/boostorg/move/pull/23#issuecomment-450567663  

# [Codecov](https://codecov.io/gh/boostorg/move/pull/23?src=pr&el=h1) Report  
> Merging [#23](https://codecov.io/gh/boostorg/move/pull/23?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/move/commit/1decc552eb92ebd1e76e1b66cb4bf690c3384432?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/move/pull/23/graphs/tree.svg?width=650&token=dMUaS9WR0b&height=150&src=pr)](https://codecov.io/gh/boostorg/move/pull/23?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #23   +/-   ##  
========================================  
  Coverage    72.44%   72.44%             
========================================  
  Files           28       28             
  Lines         1695     1695             
  Branches       578      578             
========================================  
  Hits          1228     1228             
- Misses         106      109    +3       
+ Partials       361      358    -3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/move/pull/23?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/move/algo/detail/adaptive\_sort\_merge.hpp](https://codecov.io/gh/boostorg/move/pull/23/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9tb3ZlL2FsZ28vZGV0YWlsL2FkYXB0aXZlX3NvcnRfbWVyZ2UuaHBw) | `67.61% <0%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/move/pull/23?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/move/pull/23?src=pr&el=footer). Last update [1decc55...31da589](https://codecov.io/gh/boostorg/move/pull/23?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2018-12-31 16:08:17 UTC  
> Url: https://github.com/boostorg/move/pull/23#issuecomment-450662805  

Thanks!

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-12-31 16:11:26 UTC  
> Url: https://github.com/boostorg/move/pull/23#issuecomment-450663175  

No worries. Thanks for the quick merge.

---

## Review 4 [Commented]

> Username: jeking3  
> Created_at: 2019-01-05 00:16:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/move/pull/23#pullrequestreview-189570985  

📁 CMakeLists.txt

```diff
  14 |+ target_include_directories(boost_move INTERFACE include)
  15 |+ 
  16 |+ target_link_libraries(boost_move
```

> Username: jeking3  
> Created_at: 2019-01-05 00:16:47 UTC  
> Url: https://github.com/boostorg/move/pull/23#discussion_r245454563  

How does this static list of dependencies stay valid over time?

> Username: Mike-Devel  
> Created_at: 2019-01-05 07:25:13 UTC  
> Updated_at: 2019-01-05 07:25:14 UTC  
> Url: https://github.com/boostorg/move/pull/23#discussion_r245470021  

Good question. Responded in https://github.com/boostorg/logic/pull/14#issuecomment-451634586


---
