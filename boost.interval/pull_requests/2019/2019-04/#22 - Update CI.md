# #22 Update CI [Merged]

> Username: jeking3  
> Created at: 2019-04-27 13:04:51 UTC  
> Updated at: 2019-05-03 16:13:44 UTC  
> Merged at: 2019-05-03 16:13:41 UTC  
> Closed at: 2019-05-03 16:13:41 UTC  
> Url: https://github.com/boostorg/interval/pull/22  



---

## Comment 1

> Username: jeking3  
> Created_at: 2019-05-02 10:43:20 UTC  
> Url: https://github.com/boostorg/interval/pull/22#issuecomment-488627475  

gcc 8.2.0 on mingw64 does not define `__i386__`.  That sounds like a regression.  It does define `__x86_64__` so I will be able to fix the appveyor build issue.  
  
```  
Jim@pulsar MINGW64 /c/boost/libs/numeric/interval  
$  "g++"   -fvisibility-inlines-hidden -m64 -mthreads -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -frounding-math -DBOOST_ALL_NO_LIB=1 -DNDEBUG  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\numeric\interval\test\integer.test\gcc-8.2.0\rls\thrd-mlt\vsblt-hdn\integer.o" "C:\boost\libs\numeric\interval\test\integer.cpp"  
In file included from ..\..\../boost/numeric/interval/detail/x86_rounding_control.hpp:15,  
                 from ..\..\../boost/numeric/interval/hw_rounding.hpp:23,  
                 from ..\..\../boost/numeric/interval.hpp:18,  
                 from C:\boost\libs\numeric\interval\test\integer.cpp:11:  
..\..\../boost/numeric/interval/detail/x86gcc_rounding_control.hpp:19:4: error: #error This header only works on x86 CPUs.  
 #  error This header only works on x86 CPUs.  
    ^~~~~  
  
Jim@pulsar MINGW64 /c/boost/libs/numeric/interval  
$  "g++"   -fvisibility-inlines-hidden -m64 -mthreads -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -frounding-math -DBOOST_ALL_NO_LIB=1 -DNDEBUG -dM -E -x c++ - < /dev/null | grep 'x86' | sort  
#define __x86_64 1  
#define __x86_64__ 1  
  
Jim@pulsar MINGW64 /c/boost/libs/numeric/interval  
$ g++ --version  
g++.exe (Rev3, Built by MSYS2 project) 8.2.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-05-02 11:06:29 UTC  
> Updated_at: 2019-05-03 16:02:51 UTC  
> Url: https://github.com/boostorg/interval/pull/22#issuecomment-488632720  

# [Codecov](https://codecov.io/gh/boostorg/interval/pull/22?src=pr&el=h1) Report  
> Merging [#22](https://codecov.io/gh/boostorg/interval/pull/22?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/interval/commit/802aef1264008754e03f4a6244b9ccdd88328371?src=pr&el=desc) will **increase** coverage by `0.84%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/interval/pull/22/graphs/tree.svg?width=650&token=GejkQK9wH8&height=150&src=pr)](https://codecov.io/gh/boostorg/interval/pull/22?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #22      +/-   ##  
===========================================  
+ Coverage    56.53%   57.37%   +0.84%       
===========================================  
  Files           20       19       -1       
  Lines          865      861       -4       
  Branches       358      366       +8       
===========================================  
+ Hits           489      494       +5       
- Misses         183      186       +3       
+ Partials       193      181      -12  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/interval/pull/22?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/numeric/interval/arith3.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL2FyaXRoMy5ocHA=) | `35% <0%> (-5%)` | :arrow_down: |  
| [include/boost/numeric/interval/checking.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL2NoZWNraW5nLmhwcA==) | `42.85% <0%> (-3.3%)` | :arrow_down: |  
| [include/boost/numeric/interval/interval.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL2ludGVydmFsLmhwcA==) | `70.53% <0%> (-2.04%)` | :arrow_down: |  
| [include/boost/numeric/interval/rounded\_arith.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL3JvdW5kZWRfYXJpdGguaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/numeric/interval/hw\_rounding.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL2h3X3JvdW5kaW5nLmhwcA==) | | |  
| [include/boost/numeric/interval/arith2.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL2FyaXRoMi5ocHA=) | `53.4% <0%> (+1.13%)` | :arrow_up: |  
| [include/boost/numeric/interval/detail/division.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL2RldGFpbC9kaXZpc2lvbi5ocHA=) | `30.98% <0%> (+2.81%)` | :arrow_up: |  
| [include/boost/numeric/interval/arith.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL2FyaXRoLmhwcA==) | `47.22% <0%> (+4.16%)` | :arrow_up: |  
| [include/boost/numeric/interval/utility.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL3V0aWxpdHkuaHBw) | `56.75% <0%> (+5.4%)` | :arrow_up: |  
| [...clude/boost/numeric/interval/detail/test\_input.hpp](https://codecov.io/gh/boostorg/interval/pull/22/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL2ludGVydmFsL2RldGFpbC90ZXN0X2lucHV0LmhwcA==) | `92.85% <0%> (+21.42%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/interval/pull/22?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/interval/pull/22?src=pr&el=footer). Last update [802aef1...05e1c0b](https://codecov.io/gh/boostorg/interval/pull/22?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
