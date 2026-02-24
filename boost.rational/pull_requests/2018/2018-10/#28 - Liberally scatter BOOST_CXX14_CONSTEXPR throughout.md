# #28 Liberally scatter BOOST_CXX14_CONSTEXPR throughout [Merged]

> Username: tonyelewis  
> Created at: 2018-10-27 20:05:12 UTC  
> Updated at: 2018-11-01 19:50:45 UTC  
> Merged at: 2018-10-29 20:23:36 UTC  
> Closed at: 2018-10-29 20:23:36 UTC  
> Url: https://github.com/boostorg/rational/pull/28  

Add `BOOST_CXX14_CONSTEXPR` throughout the code.

---

## Comment 1

> Username: tonyelewis  
> Created_at: 2018-10-27 20:10:53 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-433651599  

This is discussed in #29.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-10-27 20:22:41 UTC  
> Updated_at: 2018-10-27 20:23:28 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-433652371  

CI as it stands right now will not properly test this.  Could you pull in the expanded `.travis.yml` from Boost.Assign?  Nothing else needed, just copy the file in from develop tip.  That will greatly expand the CI to include many more C++ language levels.

---

## Review 3 [Changes requested]

> Username: jeking3  
> Created_at: 2018-10-27 20:23:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/rational/pull/28#pullrequestreview-169069109  

CI needs updating to properly test.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-10-28 01:57:38 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-433669806  

```  
test/rational_test.cpp:1296:5:   instantiated from here  
../../boost/rational.hpp:178:91: internal compiler error: in build_data_member_initialization, at cp/semantics.c:5553  
Please submit a full bug report,  
with preprocessed source if appropriate.  
See <file:///usr/share/doc/gcc-4.6/README.Bugs> for instructions.  
Preprocessed source stored into /tmp/cctRuDKY.out file, please attach this to your bugreport.  
    "g++-4.6"   -std=c++0x -fvisibility-inlines-hidden -fPIC -m64 -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_STATIC_LINK=1 -DNDEBUG  -I"../.." -c -o "../../bin.v2/libs/rational/test/rational_test.test/gcc-4.6/release/cxxstd-0x-iso/visibility-hidden/rational_test.o" "test/rational_test.cpp"  
...failed gcc.compile.c++ ../../bin.v2/libs/rational/test/rational_test.test/gcc-4.6/release/cxxstd-0x-iso/visibility-hidden/rational_test.o...  
```

---

## Comment 5

> Username: tonyelewis  
> Created_at: 2018-10-28 05:13:56 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-433677196  

So it looks like there was an internal compiler error in GCC 4.6 and problems in the test framework in a UBSan build. Neither of these look like direct consequences of the changes. I've submitted #30 in which I've copied the `.travis.yml` from Boost Assign to check that these tests were passing without this PR's changes.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-10-28 08:14:36 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-433685409  

CI failures not withstanding, yes please to more constexpr!  
  
I'm sure there are other C++11/14 features that can be leveraged here (noexcept, moves?)

---

## Comment 7

> Username: tonyelewis  
> Created_at: 2018-10-29 17:50:44 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-434011662  

From what I can see, PR #32 also experiences the same failures so I don't think the changes in this PR's original commit are making anything worse.

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-10-29 18:46:09 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-434033067  

Thanks, let's go back to this PR without the expanded CI and I will merge it, and we'll deal with the expanded CI separately.

---

## Comment 9

> Username: tonyelewis  
> Created_at: 2018-10-29 19:29:25 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-434047456  

OK. Any preference whether I force-remove the last commit or add another commit that reverts it?

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-10-29 20:00:22 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-434057317  

I would remove it entirely through a rebase, dropping it, and force push.

---

## Comment 11

> Username: codecov[bot]  
> Created_at: 2018-10-29 20:08:45 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-434060045  

# [Codecov](https://codecov.io/gh/boostorg/rational/pull/28?src=pr&el=h1) Report  
> Merging [#28](https://codecov.io/gh/boostorg/rational/pull/28?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/rational/commit/2c7fe1aae7fdb72458754d76aa2d0d57542264b7?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/rational/pull/28/graphs/tree.svg?width=650&token=GWRPyIttVH&height=150&src=pr)](https://codecov.io/gh/boostorg/rational/pull/28?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #28   +/-   ##  
========================================  
  Coverage    67.52%   67.52%             
========================================  
  Files            1        1             
  Lines          271      271             
  Branches        90       90             
========================================  
  Hits           183      183             
  Misses           3        3             
  Partials        85       85  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/rational/pull/28?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/rational.hpp](https://codecov.io/gh/boostorg/rational/pull/28/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9yYXRpb25hbC5ocHA=) | `67.52% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/rational/pull/28?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/rational/pull/28?src=pr&el=footer). Last update [2c7fe1a...8fd02ac](https://codecov.io/gh/boostorg/rational/pull/28?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 12

> Username: tonyelewis  
> Created_at: 2018-10-29 20:11:08 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-434060864  

OK. I've removed the last commit entirely.

---

## Comment 13

> Username: tonyelewis  
> Created_at: 2018-11-01 19:50:45 UTC  
> Url: https://github.com/boostorg/rational/pull/28#issuecomment-435164506  

Thanks. I've just submitted PR #32 that follows up on some of these issues.

---
