# #461 Integrate Beast interface target [Closed]

> Username: xsacha  
> Created at: 2017-06-10 04:11:54 UTC  
> Updated at: 2017-07-22 03:10:07 UTC  
> Closed at: 2017-06-16 16:21:34 UTC  
> Url: https://github.com/boostorg/beast/pull/461  

Adds a target (partial of #443)  
  
Does not install the target.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-06-10 16:32:28 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307575784  

While these changes look like they do what is intended, this is why I think we should not merge them:  
  
* They have little meaning when Beast becomes part of Boost  
  
* The build options and install target are not part of my workflow, and not part of the continuous integration scripts, so they will not receive testing. I won't know if I break them. Which means, they will break.

---

## Comment 2

> Username: xsacha  
> Created_at: 2017-06-10 22:05:22 UTC  
> Updated_at: 2017-06-10 22:07:43 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307592917  

Once part of Boost, the target will be part of the CMake FindBoost script.  
Would it be a separate module still like Beast or Network or part of system?  
  
The target can be used throughout your tests and examples. So you will know if it breaks.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2017-06-11 02:51:04 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307602622  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`master@18a8ef5`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/461/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff            @@  
##             master     #461   +/-   ##  
=========================================  
  Coverage          ?   93.36%             
=========================================  
  Files             ?       91             
  Lines             ?     6719             
  Branches          ?        0             
=========================================  
  Hits              ?     6273             
  Misses            ?      446             
  Partials          ?        0  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=footer). Last update [18a8ef5...172fb6a](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2017-06-11 02:51:04 UTC  
> Updated_at: 2017-06-15 05:02:08 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307602623  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=h1) Report  
> Merging [#461](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/8ba182cb2e0d073724be170cb64cd3d9226f161f?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/461/graphs/tree.svg?height=150&src=pr&token=Gq6MFA9JRF&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           master     #461   +/-   ##  
=======================================  
  Coverage   93.76%   93.76%             
=======================================  
  Files          96       96             
  Lines        7045     7045             
=======================================  
  Hits         6606     6606             
  Misses        439      439  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <0%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=footer). Last update [8ba182c...f83c275](https://codecov.io/gh/vinniefalco/Beast/pull/461?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: xsacha  
> Created_at: 2017-06-11 03:42:38 UTC  
> Updated_at: 2017-06-11 03:42:49 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307604254  

Added a new commit that uses targets throughout to simplify CMakeLists.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-06-11 16:10:59 UTC  
> Updated_at: 2017-06-11 16:11:19 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307639229  

I put together a branch based on your changes: https://github.com/vinniefalco/Beast/commits/cmake  
  
However, I get errors when I run CMake on Windows:  
```  
$ cmake ..  
-- Boost version: 1.64.0  
-- Configuring done  
CMake Error at examples/CMakeLists.txt:8 (add_executable):  
  Target "echo-op" links to target "Boost::system" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
CMake Error at examples/CMakeLists.txt:8 (add_executable):  
  Target "echo-op" links to target "Boost::system" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
...  
```  
  
None of the projects are built.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-06-11 16:59:44 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307642169  

Related failed project build output:  
https://travis-ci.org/vinniefalco/Beast/jobs/241761226

---

## Comment 8

> Username: xsacha  
> Created_at: 2017-06-11 20:37:05 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307655142  

Which version of CMake is this?  
I tested using .travis.yml on CMake 3.7 (and script built boost 1.58).  
Also your Windows/MSVC doesn't have COMPONENTS?

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2017-06-11 20:43:50 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307655542  

My cmake:  
```  
$ cmake --version  
cmake version 3.8.0  
  
CMake suite maintained and supported by Kitware (kitware.com/cmake).  
```  
  
Travis is using... I am not sure. Perhaps its in the log somewhere?

---

## Comment 10

> Username: xsacha  
> Created_at: 2017-06-11 22:12:06 UTC  
> Updated_at: 2017-06-11 22:40:57 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307660379  

Looks like a bug in whichever CMake version Travis is using because it isn't automatically adding dependant targets (date_time and atomic)? I can confirm 3.7 and 3.8 do the correct thing here on Linux.  
  
As a workaround, these can be manually added to targets since they are dependencies of Thread since Boost ~v1.5 anyway.  
  
On Windows, you should probably use COMPONENTS (same as Linux). Is there a reason you changed this? Here: https://github.com/vinniefalco/Beast/commit/0128743ddadcc1ad3f7f9c5b78116ae2bbe0c68a  
  
Edit: OK, so the issue was that your date_time and atomic wasn't built at all. This is because you aren't actually using Boost::thread. If I remove that it should all work.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2017-06-11 22:54:05 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307662468  

I tried to cherry pick the commit https://github.com/vinniefalco/Beast/pull/461/commits/e9492acc19eb21944df79ad926c17b2c77a1aa1a into my branch:  
https://github.com/vinniefalco/Beast/commits/cmake  
But it has conflicts. Can you please rebase this pull request off my branch?

---

## Comment 12

> Username: xsacha  
> Created_at: 2017-06-11 22:55:19 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307662538  

I need to do some more work on it. It looks like it does use the thread library but just has not built date_time and atomic so I need to tell the interface not to use it.

---

## Comment 13

> Username: xsacha  
> Created_at: 2017-06-11 23:38:01 UTC  
> Updated_at: 2017-06-11 23:43:05 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307664555  

OK. That's all working now. Sorry about spamming travis but I didn't want to recompile Boost 1.58 again, without date_time/atomic.  
  
Unfortunately all the disable options for date_time and atomic weren't working so I just imported empty targets.  
  
Edit: Just confirming tests work because I rebased on CMake branch and not sure it is same code I had.

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2017-06-12 00:10:15 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307666101  

This is looking much better now!

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2017-06-12 00:42:31 UTC  
> Updated_at: 2017-06-12 00:54:36 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-307667879  

This will go in to **v55**

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2017-06-13 05:09:32 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308008276  

Exactly what I was afraid of, has happened as a result of this merge. The changes break homebrew-installed Boost on Mac OS with apple's clang. And I didn't know because that is not a system that I test with, and it is not configured on the CI scripts. This has to be rolled back in v56, it has broken some users.

---

## Comment 17

> Username: xsacha  
> Created_at: 2017-06-13 05:40:38 UTC  
> Updated_at: 2017-06-13 05:56:04 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308012262  

Is there a way to test this scenario?  
Is there an error log?  
  
It is probably not too hard to fix.  
  
If boost-brew currently works, via find_package, then it should also have target imports. Were they using some alternative code before?

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2017-06-13 12:35:43 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308101667  

I have simply removed these two lines in **v56**  
```  
set (Boost_NO_SYSTEM_PATHS ON)  
set (Boost_USE_MULTITHREADED ON)  
```  
  
This is the problem with the changes you proposed:  
![image](https://user-images.githubusercontent.com/1503976/27082516-fdbb8408-4ff9-11e7-8a28-77a7125a9747.png)  
  
I don't mind adding this INTERFACE thing, if we can do it without relying on imported targets.

---

## Comment 19

> Username: xsacha  
> Created_at: 2017-06-13 13:02:44 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308108321  

Ok. Thanks for the info. That makes sense.  
  
I can submit without Boost and SSL targets as original. I had added just to simplify the list.

---

## Comment 20

> Username: vinniefalco  
> Created_at: 2017-06-13 13:06:27 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308109351  

In principle I like your change with the imported targets. But in practice, it suffers because of the issues between Boost and CMake.

---

## Comment 21

> Username: vinniefalco  
> Created_at: 2017-06-13 13:07:01 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308109478  

Is removing these two lines the right thing to do?  
```  
set (Boost_NO_SYSTEM_PATHS ON)  
set (Boost_USE_MULTITHREADED ON)  
```

---

## Comment 22

> Username: xsacha  
> Created_at: 2017-06-13 13:16:34 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308111979  

Not the multithreaded.  
Added another commit.

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2017-06-13 13:41:44 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308119566  

`Boost_USE_MULTITHREADED` defaults to on:  
https://lists.gnu.org/archive/html/discuss-gnuradio/2011-12/txta1SOQqu6kA.txt  
  
What is the reason to manually set it to ON?

---

## Comment 24

> Username: vinniefalco  
> Created_at: 2017-06-13 16:03:28 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308165951  

**v56** has your change, thanks

---

## Comment 25

> Username: xsacha  
> Created_at: 2017-06-13 23:21:26 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308276082  

Ah, I didn't know multithreaded was on by default.  
I set it on everywhere too.

---

## Comment 26

> Username: vinniefalco  
> Created_at: 2017-06-13 23:49:34 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308280158  

Does **v56** look good to you? Its in the pull request queue

---

## Comment 27

> Username: xsacha  
> Created_at: 2017-06-13 23:59:07 UTC  
> Updated_at: 2017-06-13 23:59:58 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308281449  

Looks fine to me but MSVC still grabs all of Boost instead of COMPONENTS. I hope that doesn't affect it findings Boost_SYSTEM_LIBRARY.  
If it all compiles fine, can leave that to another day.  
  
Does AppVeyor not test the CMake path?

---

## Comment 28

> Username: xsacha  
> Created_at: 2017-06-14 11:31:23 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308403982  

I have redone the part of the commit that didn't make it in without using Boost targets.

---

## Comment 29

> Username: vinniefalco  
> Created_at: 2017-06-14 23:43:31 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308588757  

Its in **v57** now

---

## Comment 30

> Username: vinniefalco  
> Created_at: 2017-06-15 00:05:29 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308591732  

@xsacha Something changes with the warnings and now CI fails  
https://travis-ci.org/vinniefalco/Beast/jobs/243058513

---

## Comment 31

> Username: vinniefalco  
> Created_at: 2017-06-15 00:07:48 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308592037  

I see it. You removed:  
```  
include_directories (SYSTEM ${Boost_INCLUDE_DIRS})  
```  
  
The "SYSTEM" keyword is special, it informs gcc and clang that headers in that directory are considered part of the system headers, and warnings should be suppressed for them.

---

## Comment 32

> Username: xsacha  
> Created_at: 2017-06-15 00:33:22 UTC  
> Updated_at: 2017-06-15 00:34:01 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308595474  

Fixed warnings. I didn't know that was intentional.

---

## Comment 33

> Username: vinniefalco  
> Created_at: 2017-06-15 01:22:44 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308602090  

Still causes warnings and build failure, see the last item in the matrix:  
https://travis-ci.org/vinniefalco/Beast/builds/243073581

---

## Comment 34

> Username: xsacha  
> Created_at: 2017-06-15 03:13:09 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308616616  

This one tested fine on .travis.yml here.

---

## Comment 35

> Username: vinniefalco  
> Created_at: 2017-06-15 03:19:38 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308617411  

Is that different?

---

## Comment 36

> Username: xsacha  
> Created_at: 2017-06-15 03:23:03 UTC  
> Updated_at: 2017-06-15 03:25:25 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308617787  

Yeah apparently some ongoing bug in CMake. The SYSTEM keyword does not send it to -isystem as you would expect but manually setting the properties works.  
See: https://github.com/MoMEMta/MoMEMta/blob/master/CMakeLists.txt#L83  
Can't find anything concrete on this solution but it works in all CMake versions whereas the documented solution does not.  
  
https://github.com/ruslo/hunter/issues/368

---

## Comment 37

> Username: vinniefalco  
> Created_at: 2017-06-15 03:25:59 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308618145  

I'm going to have to put this pull request on hold, because I am in the middle of a large refactoring of all the examples. The directories are moving around as are the CMakeLists.txt. So bringing this in would create a lot more work for me than I can handle right now. This is the branch:  
https://github.com/vinniefalco/Beast/commits/v58

---

## Comment 38

> Username: vinniefalco  
> Created_at: 2017-06-15 03:44:05 UTC  
> Updated_at: 2017-06-15 03:44:11 UTC  
> Url: https://github.com/boostorg/beast/pull/461#issuecomment-308620342  

Version 58 is in PR now, please rebase to https://github.com/vinniefalco/Beast/pull/492

---
