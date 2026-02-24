# #874 STL Charconv Integration [Closed]

> Username: mborland  
> Created at: 2023-03-21 02:53:52 UTC  
> Updated at: 2023-05-22 15:59:11 UTC  
> Closed at: 2023-05-22 15:59:11 UTC  
> Url: https://github.com/boostorg/json/pull/874  

Use the STL implementations of `<charconv>` when available. Integers are pretty well supported, but floating points are not.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-21 03:10:32 UTC  
> Updated_at: 2023-03-22 16:32:00 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1477222995  

## [Codecov](https://codecov.io/gh/boostorg/json/pull/874?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#874](https://codecov.io/gh/boostorg/json/pull/874?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4df7088) into [develop](https://codecov.io/gh/boostorg/json/commit/4d4d5fc14e25f4eb40dd091edb66656383d26fd6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4d4d5fc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/874/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/874?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #874      +/-   ##  
===========================================  
- Coverage    99.19%   99.19%   -0.01%       
===========================================  
  Files           70       70                
  Lines         6853     6851       -2       
===========================================  
- Hits          6798     6796       -2       
  Misses          55       55                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/874?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/format.ipp](https://codecov.io/gh/boostorg/json/pull/874?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2Zvcm1hdC5pcHA=) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://codecov.io/gh/boostorg/json/pull/874/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/json/pull/874?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/874?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4d4d5fc...4df7088](https://codecov.io/gh/boostorg/json/pull/874?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-03-21 04:16:13 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1477259741  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/874/pullrequest-condensed-24fe562.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/874/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/874/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-03-21 06:30:00 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1477342389  

Given that this doesn't increase performance (although, maybe it does and we just test with `<charconv>` disabled?), and does not reduce the amount of code to maintain, what would be the purpose of this change?

---

## Comment 4

> Username: mborland  
> Created_at: 2023-03-21 15:13:05 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1478017269  

> Given that this doesn't increase performance (although, maybe it does and we just test with `<charconv>` disabled?), and does not reduce the amount of code to maintain, what would be the purpose of this change?  
  
If there is no change or negative performance this would certainly not be worth merging. I'll write up a benchmark file with google benchmark so we can test with modern compilers and c++ standards.

---

## Comment 5

> Username: grisumbras  
> Created_at: 2023-03-21 15:17:21 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1478024684  

Just build `b2 libs/json/bench` with appropriate flags and run the built executable with data from `json/bench/data`. E.g. `path/to/bench -n:10 -i:b libs/bench/data/*`

---

## Comment 6

> Username: mborland  
> Created_at: 2023-03-21 20:51:18 UTC  
> Updated_at: 2023-03-21 20:53:40 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1478562758  

Here are the benchmarks for numbers. The platform is Apple M1 with homebrew GCC 12 using C++20:  
  
```  
Using built-in specs.  
COLLECT_GCC=gcc-12  
COLLECT_LTO_WRAPPER=/opt/homebrew/Cellar/gcc/12.2.0/bin/../libexec/gcc/aarch64-apple-darwin22/12/lto-wrapper  
Target: aarch64-apple-darwin22  
Configured with: ../configure --prefix=/opt/homebrew/opt/gcc --libdir=/opt/homebrew/opt/gcc/lib/gcc/current --disable-nls --enable-checking=release --with-gcc-major-version-only --enable-languages=c,c++,objc,obj-c++,fortran --program-suffix=-12 --with-gmp=/opt/homebrew/opt/gmp --with-mpfr=/opt/homebrew/opt/mpfr --with-mpc=/opt/homebrew/opt/libmpc --with-isl=/opt/homebrew/opt/isl --with-zstd=/opt/homebrew/opt/zstd --with-pkgversion='Homebrew GCC 12.2.0' --with-bugurl=https://github.com/Homebrew/homebrew-core/issues --with-system-zlib --build=aarch64-apple-darwin22 --with-sysroot=/Library/Developer/CommandLineTools/SDKs/MacOSX13.sdk  
Thread model: posix  
Supported LTO compression algorithms: zlib zstd  
gcc version 12.2.0 (Homebrew GCC 12.2.0)  
```  
  
On current develop (4d4d5fc14e25f4eb40dd091edb66656383d26fd6):  
  
```  
Parse data/numbers.json,gcc x64,boost (pool),40000,5037,1137  
Parse data/numbers.json,gcc x64,boost (pool),39700,5012,1134  
Parse data/numbers.json,gcc x64,boost (pool),41580,5246,1135  
Parse data/numbers.json,gcc x64,boost (pool),41601,5233,1138  
Parse data/numbers.json,gcc x64,boost (pool),39740,5049,1127  
Parse data/numbers.json,gcc x64,boost (pool),41307,5189,1140  
Parse data/numbers.json,gcc x64,boost (pool),39800,5027,1134  
Parse data/numbers.json,gcc x64,boost (pool),41559,5237,1136  
Parse data/numbers.json,gcc x64,boost (pool),39660,5001,1135  
Parse data/numbers.json,gcc x64,boost (pool),39640,5015,1132  
Serialize data/numbers.json,gcc x64,boost (pool),19000,5207,522  
Serialize data/numbers.json,gcc x64,boost (pool),18240,5016,521  
Serialize data/numbers.json,gcc x64,boost (pool),19089,5228,523  
Serialize data/numbers.json,gcc x64,boost (pool),18240,5028,519  
Serialize data/numbers.json,gcc x64,boost (pool),19026,5189,525  
Serialize data/numbers.json,gcc x64,boost (pool),19236,5225,527  
Serialize data/numbers.json,gcc x64,boost (pool),18400,5024,524  
Serialize data/numbers.json,gcc x64,boost (pool),18300,5035,520  
Serialize data/numbers.json,gcc x64,boost (pool),19068,5231,522  
Serialize data/numbers.json,gcc x64,boost (pool),18220,5005,521  
  
Parse data/numbers.json,gcc x64,boost (pool),1134  
Serialize data/numbers.json,gcc x64,boost (pool),521  
```  
  
With this PR:  
```  
Parse data/numbers.json,gcc x64,boost (pool),41000,5122,1146  
Parse data/numbers.json,gcc x64,boost (pool),40020,5009,1144  
Parse data/numbers.json,gcc x64,boost (pool),39940,5005,1142  
Parse data/numbers.json,gcc x64,boost (pool),41895,5219,1149  
Parse data/numbers.json,gcc x64,boost (pool),42126,5247,1149  
Parse data/numbers.json,gcc x64,boost (pool),40140,5001,1149  
Parse data/numbers.json,gcc x64,boost (pool),42126,5248,1149  
Parse data/numbers.json,gcc x64,boost (pool),40120,5003,1148  
Parse data/numbers.json,gcc x64,boost (pool),42084,5231,1152  
Parse data/numbers.json,gcc x64,boost (pool),40220,5010,1149  
Serialize data/numbers.json,gcc x64,boost (pool),16000,5228,438  
Serialize data/numbers.json,gcc x64,boost (pool),16065,5229,440  
Serialize data/numbers.json,gcc x64,boost (pool),16128,5243,440  
Serialize data/numbers.json,gcc x64,boost (pool),16149,5244,441  
Serialize data/numbers.json,gcc x64,boost (pool),16149,5244,441  
Serialize data/numbers.json,gcc x64,boost (pool),15380,5080,433  
Serialize data/numbers.json,gcc x64,boost (pool),15876,5152,441  
Serialize data/numbers.json,gcc x64,boost (pool),15400,5028,439  
Serialize data/numbers.json,gcc x64,boost (pool),16065,5224,440  
Serialize data/numbers.json,gcc x64,boost (pool),16128,5239,441  
  
Parse data/numbers.json,gcc x64,boost (pool),1148  
Serialize data/numbers.json,gcc x64,boost (pool),440  
```  
  
I am not sure what these numbers mean because there are no labels.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-03-21 21:49:41 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1478631443  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/874/pullrequest-condensed-84e6f2d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/874/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/874/pullrequest.html)

---

## Comment 8

> Username: grisumbras  
> Created_at: 2023-03-22 12:38:50 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1479500516  

The last number is the important one. Bigger is better.  
  
Seems like the change makes serialisation slower. Try without temporary buffer. The implementation ensures that there's enough space for a number.

---

## Comment 9

> Username: mborland  
> Created_at: 2023-03-22 15:54:54 UTC  
> Url: https://github.com/boostorg/json/pull/874#issuecomment-1479833155  

Here are the results without a temporary buffer.  
  
```  
Parse data/numbers.json,gcc x64,boost (pool),41000,5119,1147  
Parse data/numbers.json,gcc x64,boost (pool),42042,5242,1148  
Parse data/numbers.json,gcc x64,boost (pool),42105,5223,1154  
Parse data/numbers.json,gcc x64,boost (pool),40300,5045,1144  
Parse data/numbers.json,gcc x64,boost (pool),39940,5010,1141  
Parse data/numbers.json,gcc x64,boost (pool),41853,5231,1145  
Parse data/numbers.json,gcc x64,boost (pool),40000,5017,1141  
Parse data/numbers.json,gcc x64,boost (pool),41853,5244,1143  
Parse data/numbers.json,gcc x64,boost (pool),41895,5215,1150  
Parse data/numbers.json,gcc x64,boost (pool),40160,5021,1145  
Serialize data/numbers.json,gcc x64,boost (pool),17000,5297,459  
Serialize data/numbers.json,gcc x64,boost (pool),16842,5238,460  
Serialize data/numbers.json,gcc x64,boost (pool),16060,5012,459  
Serialize data/numbers.json,gcc x64,boost (pool),16821,5238,460  
Serialize data/numbers.json,gcc x64,boost (pool),16040,5018,458  
Serialize data/numbers.json,gcc x64,boost (pool),16779,5245,458  
Serialize data/numbers.json,gcc x64,boost (pool),16779,5221,460  
Serialize data/numbers.json,gcc x64,boost (pool),16863,5220,463  
Serialize data/numbers.json,gcc x64,boost (pool),16947,5246,463  
Serialize data/numbers.json,gcc x64,boost (pool),16140,5001,462  
  
Parse data/numbers.json,gcc x64,boost (pool),1144  
Serialize data/numbers.json,gcc x64,boost (pool),460  
```

---
