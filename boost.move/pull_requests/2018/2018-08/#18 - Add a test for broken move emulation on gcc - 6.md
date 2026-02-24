# #18 Add a test for broken move emulation on gcc < 6 [Closed]

> Username: bc-lee  
> Created at: 2018-08-23 22:12:55 UTC  
> Updated at: 2019-01-07 07:58:48 UTC  
> Closed at: 2019-01-07 07:58:42 UTC  
> Url: https://github.com/boostorg/move/pull/18  

This is an example for  broken move emulation on gcc < 6. (Issue #16 )  
  
```  
gcc.compile.c++ ../../bin.v2/libs/move/test/move_noinline.test/gcc-5/debug/move_noinline.o  
test/move_noinline.cpp:27:14: error: prototype for 'moveable_v2& moveable_v2::operator=(boost::rv<moveable_v2>&)' does not match any in class 'moveable_v2'  
 moveable_v2& moveable_v2::operator=(BOOST_RV_REF(moveable_v2) m)  
              ^  
test/move_noinline.cpp:18:18: error: candidates are: moveable_v2& moveable_v2::operator=(boost::rv<moveable_v2>&)  
     moveable_v2& operator=(BOOST_RV_REF(moveable_v2) m);  
                  ^  
In file included from test/move_noinline.cpp:1:0:  
../../boost/move/core.hpp:38:13: error:                 moveable_v2& moveable_v2::operator=(moveable_v2&)  
       TYPE& operator=(TYPE &);\  
```  
  
Tested with gcc 4.9, 5, 6, 7 in Ubuntu 16.04.

---

## Review 1 [Changes requested]

> Username: jeking3  
> Created_at: 2018-08-29 02:33:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/move/pull/18#pullrequestreview-150383447  

Thanks - appreciate the test-driven development approach.  Cannot merge this until there's a code fix however.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-12-12 05:35:12 UTC  
> Updated_at: 2018-12-17 08:34:52 UTC  
> Url: https://github.com/boostorg/move/pull/18#issuecomment-446468028  

# [Codecov](https://codecov.io/gh/boostorg/move/pull/18?src=pr&el=h1) Report  
> Merging [#18](https://codecov.io/gh/boostorg/move/pull/18?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/move/commit/1decc552eb92ebd1e76e1b66cb4bf690c3384432?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/move/pull/18/graphs/tree.svg?width=650&token=dMUaS9WR0b&height=150&src=pr)](https://codecov.io/gh/boostorg/move/pull/18?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #18   +/-   ##  
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
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/move/pull/18?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/move/algo/detail/adaptive\_sort\_merge.hpp](https://codecov.io/gh/boostorg/move/pull/18/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9tb3ZlL2FsZ28vZGV0YWlsL2FkYXB0aXZlX3NvcnRfbWVyZ2UuaHBw) | `67.61% <0%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/move/pull/18?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/move/pull/18?src=pr&el=footer). Last update [1decc55...e2c7a60](https://codecov.io/gh/boostorg/move/pull/18?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 3 [Changes requested]

> Username: glenfe  
> Created_at: 2018-12-12 12:46:50 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/move/pull/18#pullrequestreview-184159635  

📁 test/move_noinline.cpp

```diff
  52 |+     BOOST_TEST(m.moved());
  53 |+     BOOST_TEST_NOT(m2.moved());
  54 |+     return 0;
```

> Username: glenfe  
> Created_at: 2018-12-12 12:46:43 UTC  
> Updated_at: 2018-12-17 08:09:40 UTC  
> Url: https://github.com/boostorg/move/pull/18#discussion_r241000013  

This should be `return boost::report_errors();` instead of `return 0;`. That's why your new test is failing.

> Username: bc-lee  
> Created_at: 2018-12-17 08:10:41 UTC  
> Url: https://github.com/boostorg/move/pull/18#discussion_r242052901  

Fixed.


---

## Comment 4

> Username: igaztanaga  
> Created_at: 2018-12-31 16:13:17 UTC  
> Url: https://github.com/boostorg/move/pull/18#issuecomment-450663367  

Is this still a problem in Boost 1.69? Problematic MAY_ALIAS attribute was removed for GCC > 4.5.

---

## Comment 5

> Username: bc-lee  
> Created_at: 2019-01-07 07:58:42 UTC  
> Url: https://github.com/boostorg/move/pull/18#issuecomment-451850785  

I confirm that this problem is solved in Boost 1.69. Closing.

---
