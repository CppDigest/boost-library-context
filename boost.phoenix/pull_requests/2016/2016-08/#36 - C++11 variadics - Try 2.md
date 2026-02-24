# #36 C++11 variadics - Try 2 [Merged]

> Username: Flast  
> Created at: 2016-08-16 00:57:07 UTC  
> Updated at: 2016-08-17 19:00:39 UTC  
> Merged at: 2016-08-16 05:17:23 UTC  
> Closed at: 2016-08-16 05:17:23 UTC  
> Url: https://github.com/boostorg/phoenix/pull/36  

This PR is successor of #34 .  
  
After #34, I added two commits as workaround for MSVC, a79d2fc and 5e6e39a.  
#### Tested: (both of boost.phoenix and boost.log)  
- GCC 6.1.1 20160721 (Red Hat 6.1.1-4)  
  - gnu++98 / 11 / 14 mode  
- clang 3.8.0  
  - gnu++98 / 11 / 14 mode  
- MSVC  
  - 9.0 (2008)  
  - 10.0 (2010 SP1)  
  - 11.0 (2012 u5)  
  - 12.0 (2013 u5)  
  - 14.0 (2015 u2)

---

## Comment 1

> Username: djowel  
> Created_at: 2016-08-16 05:18:02 UTC  
> Url: https://github.com/boostorg/phoenix/pull/36#issuecomment-240003404  

Thanks Kohei. I hope it's all clear this time :)

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-08-17 12:16:21 UTC  
> Url: https://github.com/boostorg/phoenix/pull/36#issuecomment-240393805  

Looks like Spirit have been broken with this merge.  
Look [test matrix](http://www.boost.org/development/tests/develop/developer/spirit.html) for a massive failure in the most of the runs appeared on august 16.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2016-08-17 12:32:56 UTC  
> Updated_at: 2016-08-17 13:46:20 UTC  
> Url: https://github.com/boostorg/phoenix/pull/36#issuecomment-240397152  

And Phoenix tests are not passing on [GCC 4.4 with std >= c++0x](http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dg4-4-warn-Docker-64on64-boost-bin-v2-libs-phoenix-test-adapt_function-test-gcc-4-4~c++0x~warn-debug.html), so this compiler should be blacklisted from varadics support.

---

## Comment 4

> Username: Flast  
> Created_at: 2016-08-17 13:54:59 UTC  
> Updated_at: 2016-08-17 13:59:53 UTC  
> Url: https://github.com/boostorg/phoenix/pull/36#issuecomment-240418348  

> And Phoenix tests are not passing on GCC 4.4 with std >= c++0x, so this compiler should be blacklisted from for varadics support.  
  
It is fixed by #37 .  
  
> Looks like Spirit have been broken with this merge.  
> Look test matrix for a massive failure in the most of the runs appeared on august 16.  
  
Could you point (one or more failure) which is regression? I think the failure caused by this PR is only above.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2016-08-17 14:04:40 UTC  
> Url: https://github.com/boostorg/phoenix/pull/36#issuecomment-240421260  

Seems that all except one spirit fails was because of GCC <= 4.4.  
There is [failures I got](http://www.boost.org/development/tests/develop/developer/output/Kojoley-win64-clang-boost-bin-v2-libs-spirit-test-karma_alternative2-p3-test-clang-linux-3-8-1-debug.html) (`Kojoley-win64-clang` tag), but I do not know are they related to phoenix or not.

---

## Comment 6

> Username: Flast  
> Created_at: 2016-08-17 14:11:17 UTC  
> Url: https://github.com/boostorg/phoenix/pull/36#issuecomment-240423199  

```  
In file included from ..\libs\spirit\test\karma\alternative2.cpp:11:  
In file included from ..\boost/spirit/include/karma_auxiliary.hpp:16:  
In file included from ..\boost/spirit/home/karma/auxiliary.hpp:13:  
In file included from ..\boost/spirit/home/karma/auxiliary/eps.hpp:13:  
In file included from ..\boost/spirit/home/support/common_terminals.hpp:15:  
In file included from ..\boost/spirit/home/support/terminal.hpp:17:  
In file included from ..\boost/spirit/include/phoenix_core.hpp:11:  
In file included from ..\boost/phoenix/core.hpp:11:  
In file included from ..\boost/phoenix/core/limits.hpp:28:  
In file included from ..\boost/proto/proto_fwd.hpp:28:  
In file included from ..\boost/utility/result_of.hpp:22:  
..\boost/mpl/if.hpp:131:1: error: pasting formed 'BOOST_PP_TUPLE_ELEM_O_3(', an invalid preprocessing token [-Winvalid-token-paste]  
BOOST_MPL_AUX_NA_SPEC(3, if_)  
```  
  
I suspect it is not related to phoenix (and this PR) because it is too early, no header should be included prior to `core/limits.hpp`.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2016-08-17 19:00:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/36#issuecomment-240512949  

> I suspect it is not related to phoenix (and this PR) because it is too early, no header should be included prior to `core/limits.hpp`.  
  
I am sorry for disturbing you. It seems that you are right, my problem is not in a phoenix, and looks like it is already known for a long time http://lists.boost.org/Archives/boost/2013/09/206606.php

---
