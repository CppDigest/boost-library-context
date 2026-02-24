# #144 Change windows pid_t to decltype(::boost::winapi::PROCESS_INFORMATION_::dwProcessId); [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 07:09:44 UTC  
> Updated at: 2021-02-18 03:19:50 UTC  
> Closed at: 2020-05-20 14:05:01 UTC  
> Url: https://github.com/boostorg/process/pull/144  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 07:53:18 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-598595542  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/144?src=pr&el=h1) Report  
> Merging [#144](https://codecov.io/gh/boostorg/process/pull/144?src=pr&el=desc) into [master](https://codecov.io/gh/boostorg/process/commit/0341e08297512cfc3136d1324364f406518b0f11?src=pr&el=desc) will **decrease** coverage by `4.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/144/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/144?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff            @@  
##           master    #144      +/-   ##  
=========================================  
- Coverage   89.12%   85.1%   -4.03%       
=========================================  
  Files         110     110                
  Lines        3135    2564     -571       
=========================================  
- Hits         2794    2182     -612       
- Misses        341     382      +41  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/144?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0% <0%> (-100%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/detail/posix/terminate.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC90ZXJtaW5hdGUuaHBw) | `0% <0%> (-80%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `18.18% <0%> (-75.94%)` | :arrow_down: |  
| [include/boost/process/detail/posix/basic\_cmd.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9iYXNpY19jbWQuaHBw) | `27.53% <0%> (-68.12%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0% <0%> (-61.54%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60% <0%> (-40%)` | :arrow_down: |  
| [include/boost/process/detail/posix/group\_ref.hpp](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9yZWYuaHBw) | `30% <0%> (-30%)` | :arrow_down: |  
| ... and [34 more](https://codecov.io/gh/boostorg/process/pull/144/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/144?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/144?src=pr&el=footer). Last update [0341e08...61cad92](https://codecov.io/gh/boostorg/process/pull/144?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jonesmz  
> Created_at: 2020-05-20 16:19:27 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631579105  

Your commit misses half of the improvements .  
  
Please reconsider.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 16:54:02 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631598269  

Which? What's the improvement of `using` over `typedef`?

---

## Comment 4

> Username: jonesmz  
> Created_at: 2020-05-20 17:26:41 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631615904  

The use of decltype(::boost::winapi::PROCESS_INFORMATION_::dwProcessId);  
  
and the *removal* of the static cast. Keeping the static cast will result in changes to the type of dwProcessId going unaccounted for because the static cast masks it.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 17:52:22 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631629135  

`dwProcessId` literally says it's a `DWORD`. No need for the decltype.

---

## Comment 6

> Username: jonesmz  
> Created_at: 2020-05-20 18:04:31 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631635326  

Until dwProcessId changes...

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 18:37:23 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631651579  

Google hungarian notation.

---

## Comment 8

> Username: jonesmz  
> Created_at: 2020-05-20 18:45:34 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631656109  

I don't see how that's relevant.  
  
Just because hungarian notation was used when the variable was originally defined, does not in any way imply that it's type won't change in the future. In fact, it's likely that the name of the variable would be left alone to prevent causing compile errors in other people's code even if the type of it changes.  
  
You can either assume that no one will ever change the type of dwProcessId, or you can be guaranteed that your code either:  
1) Stops compiling, if dwProcessId has it's name changed  
2) Continues working perfectly if dwProcessId has it's type changed but not it's name.  
  
In either situation, using decltype is better than having the type changed out from under you without the type of pid_t being updated to match.

---

## Comment 9

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 19:00:42 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631664267  

Yes, and if the WinApi changes the type (which I would consider very unlikely) `::boost::winapi::PROCESS_INFORMATION_::dwProcessId` will need to be changed, too.

---

## Comment 10

> Username: jonesmz  
> Created_at: 2020-05-20 19:09:38 UTC  
> Url: https://github.com/boostorg/process/pull/144#issuecomment-631668951  

Right.  
  
So using decltype(::boost::winapi::PROCESS_INFORMATION_::dwProcessId)  
  
means that boost::process will not need to be updated, unless the name of dwProcessId changes.

---
