# #198 Beast install target and debian package generator. [Closed]

> Username: avalchev  
> Created at: 2016-11-23 11:14:44 UTC  
> Updated at: 2017-02-06 21:53:31 UTC  
> Closed at: 2017-02-06 21:53:30 UTC  
> Url: https://github.com/boostorg/beast/pull/198  

Additional cmake configuration to properly generate Beast package libbeast-dev on Debian (RPM should be easy too).  
  
Example usage with cmake:  
  
```  
find_package(Beast REQUIRED)  
include_directories(${Beast_INCLUDE_DIR})  
```  
  
Also cmake sets variable Beast_FOUND properly.  
  
Please notice that package maintainer and contact are set to my info. Feel free to change these.

---

## Comment 1

> Username: codecov-io  
> Created_at: 2016-11-23 12:23:50 UTC  
> Updated_at: 2017-02-06 21:51:47 UTC  
> Url: https://github.com/boostorg/beast/pull/198#issuecomment-262499801  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/198?src=pr&el=h1) Report  
> Merging [#198](https://codecov.io/gh/vinniefalco/Beast/pull/198?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/797631c698ae6ea10dcec577338b764e3ee94bd2?src=pr&el=desc) will **increase** coverage by `1.73%`.  
  
  
```diff  
@@            Coverage Diff            @@  
##           master    #198      +/-   ##  
=========================================  
+ Coverage   94.76%   96.5%   +1.73%       
=========================================  
  Files          87      83       -4       
  Lines        6229    5746     -483       
=========================================  
- Hits         5903    5545     -358       
+ Misses        326     201     -125  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/198?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/detail/decorator.hpp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2RlY29yYXRvci5ocHA=) | `94.73% <ø> (-5.27%)` | :x: |  
| [include/beast/websocket/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `87.43% <ø> (-1.03%)` | :x: |  
| [include/beast/websocket/detail/utf8_checker.hpp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `100% <ø> (ø)` | :white_check_mark: |  
| [include/beast/zlib/inflate_stream.hpp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC96bGliL2luZmxhdGVfc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :white_check_mark: |  
| [include/beast/core/impl/static_streambuf.ipp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvc3RhdGljX3N0cmVhbWJ1Zi5pcHA=) | `100% <ø> (ø)` | :white_check_mark: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :white_check_mark: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `95.18% <ø> (ø)` | :white_check_mark: |  
| [include/beast/http/impl/parse.ipp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcGFyc2UuaXBw) | `100% <ø> (ø)` | :white_check_mark: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :white_check_mark: |  
| [include/beast/websocket/detail/invokable.hpp](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ludm9rYWJsZS5ocHA=) | `75% <ø> (ø)` | :white_check_mark: |  
| ... and [30 more](https://codecov.io/gh/vinniefalco/Beast/pull/198/changes?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/198?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/198?src=pr&el=footer). Last update [797631c...8a2b796](https://codecov.io/gh/vinniefalco/Beast/compare/797631c698ae6ea10dcec577338b764e3ee94bd2...8a2b796dfe5ade093d667610be31fa65c6c26756?el=footer&src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: djarek  
> Created_at: 2016-11-23 20:41:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/198#pullrequestreview-9941722  

📁 CMakeLists.txt

```diff
 191 |+   set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)
 192 |+   set(CPACK_DEBIAN_PACKAGE_DEPENDS
 193 |+     "libc6 (>= 2.3) , libssl-dev (>=1.0.2) , libboost-coroutine-dev , libboost-context-dev , libboost-thread-dev , libboost-filesystem-dev , libboost-program-options-dev "
```

> Username: djarek  
> Created_at: 2016-11-23 20:41:44 UTC  
> Updated_at: 2016-11-24 06:04:06 UTC  
> Url: https://github.com/boostorg/beast/pull/198#discussion_r89395822  

Is there a way specify optional dependencies? I'm quite sure that libssl-dev and libboost-coroutine-dev aren't necessary.

> Username: avalchev  
> Created_at: 2016-11-24 05:54:51 UTC  
> Updated_at: 2016-11-24 06:04:06 UTC  
> Url: https://github.com/boostorg/beast/pull/198#discussion_r89438729  

Actually coroutine dependency is not optional:  
   
[find_package(Boost REQUIRED COMPONENTS coroutine context thread filesystem program_options system)  
](https://github.com/vinniefalco/Beast/blob/master/CMakeLists.txt#L28)  
  
As far as I know with Linux packaging systems there are no `optional` dependencies. But you can "suggest":  
  
> CPACK_DEBIAN_PACKAGE_SUGGESTS -  Allows packages to declare a suggested package install grouping.  
  
Two reasons I made `libssl-dev` mandatory:  
  
- I never install suggested packages.  Less packages, better system :).  
- A lot of questions from new users may emerge about SSL support.  
- `libssl-dev` is very common package for all Linux distributions, so there is no chance to be missing.  
  
For me it's not a problem to move `libssl-dev` to "Suggested" packages.  
  
Please advice how to proceed.

> Username: djarek  
> Created_at: 2016-11-25 12:45:40 UTC  
> Updated_at: 2016-11-25 12:45:41 UTC  
> Url: https://github.com/boostorg/beast/pull/198#discussion_r89612828  

I guess then it's fine to keep these as dependencies.


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-02-06 21:53:30 UTC  
> Url: https://github.com/boostorg/beast/pull/198#issuecomment-277826112  

I appreciate the contribution, but there's no way for me to maintain this since I don't build packages. And when Beast becomes part of Boost, well then this will be obsolete. I guess if Beast fails to make it into Boost then we could revisit it but for now I am regretfully closing this.

---
