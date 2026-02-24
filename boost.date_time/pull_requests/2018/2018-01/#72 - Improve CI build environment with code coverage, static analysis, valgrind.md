# #72 Improve CI build environment with code coverage, static analysis, valgrind [Merged]

> Username: jeking3  
> Created at: 2018-01-19 16:17:44 UTC  
> Updated at: 2018-01-22 01:14:15 UTC  
> Merged at: 2018-01-22 01:14:07 UTC  
> Closed at: 2018-01-22 01:14:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/72  

Adds the following to the CI build environment:  
  
- codecov.io  
- Coverity Scan  
- cppcheck  
- ubsan  
- valgrind

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-01-19 16:22:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/72#pullrequestreview-90165488  

📁 .travis.yml

```diff
  74 |+       env: 
  75 |+         - COMMENT="C++03"
  76 |+         - TOOLSET=gcc,gcc-7
```

> Username: pdimov  
> Created_at: 2018-01-19 16:22:38 UTC  
> Updated_at: 2018-01-21 15:54:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/72#discussion_r162666817  

g++ 7 is -std=gnu++14 by default if you don't pass anything; if you really want c++03, need cxxstd=03.

> Username: pdimov  
> Created_at: 2018-01-19 16:26:16 UTC  
> Updated_at: 2018-01-21 15:54:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/72#discussion_r162667866  

Just saw that you're setting it to 03 in `ci/build.sh` if not set; you can ignore that then :-) although I'd still use CXXSTD: 03 here. Testing without -std= set is a legitimate scenario that you might wish to enable.

> Username: jeking3  
> Created_at: 2018-01-19 16:41:50 UTC  
> Updated_at: 2018-01-21 15:54:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/72#discussion_r162672247  

Thanks!

> Username: jeking3  
> Created_at: 2018-01-19 16:43:31 UTC  
> Updated_at: 2018-01-21 15:54:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/72#discussion_r162672677  

This is handled in the ci/build.sh script.    
CXXSTD=03 is used if not set otherwise for this particular repo.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2018-01-19 16:23:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/72#pullrequestreview-90166040  

📁 .travis.yml

```diff
 119 |+         - B2_VARIANT=variant=debug
 120 |+         - TOOLSET=gcc-7
 121 |+         - CXXFLAGS="cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined"
```

> Username: pdimov  
> Created_at: 2018-01-19 16:23:55 UTC  
> Updated_at: 2018-01-21 15:54:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/72#discussion_r162667260  

You have to use `-fno-sanitize-recover=undefined` here; without it, the sanitizer prints messages to stderr, but the test passes and nobody's any the wiser. :-)

> Username: jeking3  
> Created_at: 2018-01-19 16:42:06 UTC  
> Updated_at: 2018-01-21 15:54:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/72#discussion_r162672305  

Thanks!  I will change this too.

> Username: jeking3  
> Created_at: 2018-01-20 06:09:27 UTC  
> Updated_at: 2018-01-21 15:54:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/72#discussion_r162775525  

@pdimov thanks for catching this; I applied it to the other repositories where I run UBSAN and it uncovered something in serialization, and as you can see in the failed build uncovered a few things in DateTime too.


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-01-22 00:06:28 UTC  
> Updated_at: 2018-01-22 00:08:55 UTC  
> Url: https://github.com/boostorg/date_time/pull/72#issuecomment-359294374  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/72?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@313c1a3`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/72/graphs/tree.svg?src=pr&width=650&token=nDoh7t8f6g&height=150)](https://codecov.io/gh/boostorg/date_time/pull/72?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #72   +/-   ##  
==========================================  
  Coverage           ?   93.71%             
==========================================  
  Files              ?       80             
  Lines              ?     5015             
  Branches           ?        0             
==========================================  
  Hits               ?     4700             
  Misses             ?      315             
  Partials           ?        0  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/72?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/72?src=pr&el=footer). Last update [313c1a3...7512833](https://codecov.io/gh/boostorg/date_time/pull/72?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
