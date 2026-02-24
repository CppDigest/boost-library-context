# #95 Fixes https://github.com/boostorg/uuid/issues/94 [Merged]

> Username: tomy2105  
> Created at: 2019-03-28 10:12:18 UTC  
> Updated at: 2019-07-23 09:40:22 UTC  
> Merged at: 2019-04-19 10:44:05 UTC  
> Closed at: 2019-04-19 10:44:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/95  

Avoid undefining BOOST_AUTO_LINK_NOMANGLE if it was previously defined.  
  
Maybe better solution would be to directly  
#pragma comment(lib, "bcrypt.lib")  
#pragma comment(lib, "advapi32.lib")  
or  
#pragma comment(lib, "coredll.lib")  
  
Since <boost/config/auto_link.hpp> seems to be oriented for various logics for linking boost libs and those 3 are not boost libs.  
  
Didn't want to go to change that big myself, so implemented one that still uses auto_link.hpp but solves the problem.  
  
This fixes #94

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-03-29 10:47:18 UTC  
> Updated_at: 2019-03-29 10:48:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/95#issuecomment-477953753  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/95?src=pr&el=h1) Report  
> Merging [#95](https://codecov.io/gh/boostorg/uuid/pull/95?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/2400f6951917cc3ad955d6850747fba48843578b?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/95/graphs/tree.svg?width=650&token=6falIr5npV&height=150&src=pr)](https://codecov.io/gh/boostorg/uuid/pull/95?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #95   +/-   ##  
========================================  
  Coverage    80.09%   80.09%             
========================================  
  Files           13       13             
  Lines          638      638             
  Branches       152      152             
========================================  
  Hits           511      511             
  Misses          17       17             
  Partials       110      110  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/95?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/95?src=pr&el=footer). Last update [2400f69...c07c1fd](https://codecov.io/gh/boostorg/uuid/pull/95?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: ColinChargyBentley  
> Created_at: 2019-07-23 09:40:22 UTC  
> Url: https://github.com/boostorg/uuid/pull/95#issuecomment-514135091  

Hi,  
In fact, the same kind of behavior can happen with BOOST_AUTO_LINK_TAGGED or BOOST_AUTO_LINK_SYSTEM enabled. For example, with BOOST_AUTO_LINK_TAGGED enabled, the linking is failing with this error :  
LINK : fatal error LNK1104: cannot open file 'libbcrypt-mt-x64.lib'  
  
I think that would be a good idea to avoid using auto_link for non-boost libs.  
Regards,  
Colin Chargy

---
