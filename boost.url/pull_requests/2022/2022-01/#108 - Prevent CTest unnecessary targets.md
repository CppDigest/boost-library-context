# #108 Prevent CTest unnecessary targets [Closed]

> Username: alandefreitas  
> Created at: 2022-01-10 19:09:06 UTC  
> Updated at: 2022-01-19 18:38:30 UTC  
> Closed at: 2022-01-12 23:33:07 UTC  
> Url: https://github.com/boostorg/url/pull/108  

`include(CTest)` includes a number of CMake internal unnecessary targets that clutter the project targets.   
  
This PR sets the option `set_property(GLOBAL PROPERTY CTEST_TARGETS_ADDED 1)` to remove these targets.  
  
Targets we are generating with the option:   
  
![image](https://user-images.githubusercontent.com/5369819/148824354-dc11e4fd-7a2e-4670-8fb3-afac0e8e74e1.png)  
  
Targets we were generating before:  
  
![image](https://user-images.githubusercontent.com/5369819/148824574-591755eb-1e17-4245-98b6-f6163532fe10.png)

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-01-10 19:09:52 UTC  
> Url: https://github.com/boostorg/url/pull/108#issuecomment-1009256052  

@grisumbras @pdimov is this any good?

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-01-10 19:12:15 UTC  
> Url: https://github.com/boostorg/url/pull/108#issuecomment-1009257900  

No idea.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-01-11 16:09:39 UTC  
> Url: https://github.com/boostorg/url/pull/108#issuecomment-1010117542  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/108?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#108](https://codecov.io/gh/CPPAlliance/url/pull/108?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (6ecb111) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/c62c4ed3d0f09cd87693f124829893e16fd3360f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (c62c4ed) will **increase** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/108/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/108?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #108      +/-   ##  
===========================================  
+ Coverage    96.36%   96.38%   +0.02%       
===========================================  
  Files           85       85                
  Lines         5336     5313      -23       
===========================================  
- Hits          5142     5121      -21       
+ Misses         194      192       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/108?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/rfc/impl/relative\_ref\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcmVsYXRpdmVfcmVmX3J1bGUuaXBw) | `70.00% <0.00%> (-2.73%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/uri\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvdXJpX3J1bGUuaXBw) | `76.92% <0.00%> (-1.65%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/authority\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvYXV0aG9yaXR5X3J1bGUuaXBw) | `92.85% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/uri\_reference\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvdXJpX3JlZmVyZW5jZV9ydWxlLmlwcA==) | `88.00% <0.00%> (-0.89%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/hier\_part\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvaGllcl9wYXJ0X3J1bGUuaXBw) | `90.90% <0.00%> (-0.27%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/userinfo\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvdXNlcmluZm9fcnVsZS5pcHA=) | `95.00% <0.00%> (-0.24%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/relative\_part\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcmVsYXRpdmVfcGFydF9ydWxlLmlwcA==) | `91.66% <0.00%> (-0.23%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/port\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcG9ydF9ydWxlLmlwcA==) | `97.22% <0.00%> (-0.15%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/host\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvaG9zdF9ydWxlLmlwcA==) | `97.14% <0.00%> (-0.08%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/paths\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcGF0aHNfcnVsZS5pcHA=) | `98.73% <0.00%> (-0.02%)` | :arrow_down: |  
| ... and [21 more](https://codecov.io/gh/CPPAlliance/url/pull/108/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/108?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/108?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [c62c4ed...6ecb111](https://codecov.io/gh/CPPAlliance/url/pull/108?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 4

> Username: friendlyanon  
> Created_at: 2022-01-12 22:01:47 UTC  
> Url: https://github.com/boostorg/url/pull/108#issuecomment-1011491018  

If you wish to personalize your experience, then you could write a CMake script somewhere with this content:  
  
```cmake  
set_property(GLOBAL PROPERTY CTEST_TARGETS_ADDED 1)  
```  
  
then inject it into the project using the [`CMAKE_PROJECT_INCLUDE_BEFORE`](https://cmake.org/cmake/help/latest/command/project.html#code-injection) variable from the command line.  
  
Alternatively, you could complain to JetBrains at https://youtrack.jetbrains.com/issue/CPP-3558 for not supporting folders. VS for example tucks things away nicely with folders enabled:  
  
![image](https://user-images.githubusercontent.com/1736896/149229762-bbd3af3e-f15c-4331-ae5e-f3a775105d25.png)

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-01-12 22:11:02 UTC  
> Url: https://github.com/boostorg/url/pull/108#issuecomment-1011497320  

`enable_testing()` seems like a more viable solution now that we know all we need from `include(CTest)` is the `ENABLE_TESTING` cache option.   
  
Hiding the unneeded targets locally with patches or folders doesn't seem to solve the problem.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-01-12 22:11:20 UTC  
> Url: https://github.com/boostorg/url/pull/108#issuecomment-1011497504  

> Hiding the unneeded targets locally with patches or folders doesn't seem to solve the problem.  
  
What happened when you tried it?

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2022-01-12 22:21:07 UTC  
> Url: https://github.com/boostorg/url/pull/108#issuecomment-1011503873  

> What happened when you tried it?  
  
I don't mean I tried to hide it and failed.   
Of course, I know how to organize and hide the targets the IDE shows me.  
  
What I mean is hiding is not a good solution globally because hiding is local, needs to be redone, and the build system still generates them even if hidden.   
These targets have nothing to do with Boost.URL, and I'm pretty sure they don't exist in Jamfile.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-01-12 23:33:07 UTC  
> Url: https://github.com/boostorg/url/pull/108#issuecomment-1011548428  

The CML is already idiomatic:  
https://github.com/CPPAlliance/url/blob/develop/CMakeLists.txt#L27

---
