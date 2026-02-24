# #116 Visual Studio 2013 support [Closed]

> Username: egor-tensin  
> Created at: 2020-10-02 22:31:14 UTC  
> Updated at: 2021-07-15 15:47:10 UTC  
> Closed at: 2021-07-15 15:47:10 UTC  
> Url: https://github.com/boostorg/nowide/pull/116  

Would you consider adding support for VS 2013, implemented in this PR? I know it's old, but I still use it on AppVeyor for some things. All the tests seem to pass, including the VS 2013 ones on AppVeyor:  
  
* https://github.com/egor-tensin/nowide/actions/runs/284973434  
* https://travis-ci.com/github/egor-tensin/nowide/builds/187913934  
* https://ci.appveyor.com/project/egor-tensin/nowide/builds/35542850  
  
It's just a few macros to abstract away things like `noexcept` and `constexpr` + some minor code changes.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-10-03 00:07:34 UTC  
> Updated_at: 2020-10-03 00:37:01 UTC  
> Url: https://github.com/boostorg/nowide/pull/116#issuecomment-703008072  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/116?src=pr&el=h1) Report  
> Merging [#116](https://codecov.io/gh/boostorg/nowide/pull/116?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/4d3afcef3cb81721502361251cd8fbbad91a8bac?el=desc) will **increase** coverage by `0.04%`.  
> The diff coverage is `93.10%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #116      +/-   ##  
===========================================  
+ Coverage    90.31%   90.36%   +0.04%       
===========================================  
  Files           27       27                
  Lines         2560     2573      +13       
  Branches       185      185                
===========================================  
+ Hits          2312     2325      +13       
  Misses         242      242                
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/116?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `81.67% <ø> (-0.61%)` | :arrow_down: |  
| [include/boost/nowide/utf8\_codecvt.hpp](https://codecov.io/gh/boostorg/nowide/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmOF9jb2RlY3Z0LmhwcA==) | `91.35% <33.33%> (ø)` | |  
| [include/boost/nowide/fstream.hpp](https://codecov.io/gh/boostorg/nowide/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZnN0cmVhbS5ocHA=) | `98.68% <100.00%> (+0.17%)` | :arrow_up: |  
| [src/filebuf.cpp](https://codecov.io/gh/boostorg/nowide/pull/116/diff?src=pr&el=tree#diff-c3JjL2ZpbGVidWYuY3Bw) | `94.73% <100.00%> (ø)` | |  
| [test/test\_stackstring.cpp](https://codecov.io/gh/boostorg/nowide/pull/116/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N0YWNrc3RyaW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_stat.cpp](https://codecov.io/gh/boostorg/nowide/pull/116/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N0YXQuY3Bw) | `100.00% <100.00%> (ø)` | |

---

## Comment 2

> Username: Flamefire  
> Created_at: 2020-10-03 07:25:12 UTC  
> Url: https://github.com/boostorg/nowide/pull/116#issuecomment-703060858  

Hello and thanks for the PR.  
  
TBH I'd actually like to avoid that. It is replacing known keywords such as `noexcept` by a long macro `BOOST_NOEXCEPT_OR_NOTHROW` and also adds some code (see `buf_holder`)  
  
MSVC 2013 is by now really old. There are even free community editions of recent versions and the codegen is much better now. So the question I'd like to have answered is: Why is this required? You mentioned you use it on CI, but are there actual usages of MSVC 2013 with Boost.Nowide? What are the reasons for that (why can't they use a newer MSVC)? I'm asking because it might be a better idea also for you to just drop MSVC 2013 too if you are using Boost.Nowide and there are no downstream users with hard requirements on that version.

---

## Comment 3

> Username: egor-tensin  
> Created_at: 2020-10-03 08:08:15 UTC  
> Url: https://github.com/boostorg/nowide/pull/116#issuecomment-703065796  

Shame. Oh well, feel free to close it then. I know my compiler requirements pretty well, and don't want to bore anyone by elaborating on them.

---

## Comment 4

> Username: egor-tensin  
> Created_at: 2021-07-15 15:47:10 UTC  
> Url: https://github.com/boostorg/nowide/pull/116#issuecomment-880806253  

Forgot to close, sorry.

---
