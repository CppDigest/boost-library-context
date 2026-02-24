# #152 [Build Fix] Workaround for a MSVC 14.1 and 14.2 compiler bug [Merged]

> Username: Naios  
> Created at: 2020-03-23 21:37:19 UTC  
> Updated at: 2020-05-20 17:32:18 UTC  
> Merged at: 2020-05-20 13:05:22 UTC  
> Closed at: 2020-05-20 13:05:23 UTC  
> Url: https://github.com/boostorg/process/pull/152  

Currently affects the MSVC CI  
  
* boost\include\boost/process/child.hpp(35): error C2600:  
    'boost::process::child::child': cannot define a compiler-generated  
    special member function (must be declared in the class first)  
* Introduced by 8541cae  
* See boostorg/process#113

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-23 21:56:00 UTC  
> Updated_at: 2020-03-23 22:04:28 UTC  
> Url: https://github.com/boostorg/process/pull/152#issuecomment-602877623  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/152?src=pr&el=h1) Report  
> Merging [#152](https://codecov.io/gh/boostorg/process/pull/152?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8&el=desc) will **decrease** coverage by `6.65%`.  
> The diff coverage is `0.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/152/graphs/tree.svg?width=650&height=150&src=pr&token=AhunMqTSpA)](https://codecov.io/gh/boostorg/process/pull/152?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #152      +/-   ##  
===========================================  
- Coverage    89.14%   82.48%   -6.66%       
===========================================  
  Files          110      111       +1       
  Lines         3132     3009     -123       
===========================================  
- Hits          2792     2482     -310       
- Misses         340      527     +187       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/152?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/detail/child\_decl.hpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jaGlsZF9kZWNsLmhwcA==) | `87.87% <0.00%> (-3.89%)` | :arrow_down: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0.00% <0.00%> (-100.00%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `15.38% <0.00%> (-78.74%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0.00% <0.00%> (-61.54%)` | :arrow_down: |  
| [test/run\_exe\_path.cpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-dGVzdC9ydW5fZXhlX3BhdGguY3Bw) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60.00% <0.00%> (-40.00%)` | :arrow_down: |  
| [test/search\_path.cpp](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree#diff-dGVzdC9zZWFyY2hfcGF0aC5jcHA=) | `63.63% <0.00%> (-36.37%)` | :arrow_down: |  
| ... and [80 more](https://codecov.io/gh/boostorg/process/pull/152/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/152?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/152?src=pr&el=footer). Last update [668579e...4477176](https://codecov.io/gh/boostorg/process/pull/152?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jonesmz  
> Created_at: 2020-03-23 23:12:00 UTC  
> Url: https://github.com/boostorg/process/pull/152#issuecomment-602907103  

Which actual Visual Studio release does this correspond to?

---

## Comment 3

> Username: jonesmz  
> Created_at: 2020-03-23 23:13:47 UTC  
> Updated_at: 2020-03-23 23:14:19 UTC  
> Url: https://github.com/boostorg/process/pull/152#issuecomment-602907760  

https://en.wikipedia.org/wiki/Microsoft_Visual_C%2B%2B#Internal_version_numbering  
  
MSVC++ 14.2  _MSC_VER == 1920 (Visual Studio 2019 Version 16.0)  
  
  
Has this bug been posted to Microsoft's community bug tracker?  
  
Edit: It has: https://developercommunity.visualstudio.com/content/problem/909914/unexpected-compilation-error-for-compiler-generate.html

---

## Comment 4

> Username: jonesmz  
> Created_at: 2020-03-23 23:15:16 UTC  
> Url: https://github.com/boostorg/process/pull/152#issuecomment-602908306  

Well, I can't stop this change from being merged, but my opinion is that this is a compiler bug. Boost::Process shouldn't work around compiler bugs.

---

## Comment 5

> Username: Naios  
> Created_at: 2020-03-24 10:05:54 UTC  
> Updated_at: 2020-03-24 10:09:12 UTC  
> Url: https://github.com/boostorg/process/pull/152#issuecomment-603145851  

I know it seems not like an optimal solution to adapt the library to a compiler bug however you also don't want to lock out the majority of the userbase from a fairly new compiler (~3 years old) because of a single line which doesn't introduce new functionality or bugfixes.  
  
Also as shown in https://ci.appveyor.com/project/klemens-morgenstern/boost-process/branch/develop#L2005 your commit also broke the MSVC CI and should not have been merged in the first place.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 13:03:56 UTC  
> Url: https://github.com/boostorg/process/pull/152#issuecomment-631460152  

I originally changed most of the constructors to work in the same way, for exactly that compiler.  Which is why many initializers have constructors like that. It's valid C++ and I do understand that not everone can update their compilers as often.

---

## Comment 7

> Username: jonesmz  
> Created_at: 2020-05-20 17:32:18 UTC  
> Url: https://github.com/boostorg/process/pull/152#issuecomment-631618803  

For me, the big sticking point here is that this isn't an issue of people being able to upgrade their compilers or not. This is an issue of Microsoft having a non-conforming compiler, for it's latest release.  
  
By adapting the code to work with Microsoft's latest bugs, you're implicitly saying that's acceptable behavior from Microsoft, and drastically reducing the number of people that would put pressure on them to fix the problem.

---
