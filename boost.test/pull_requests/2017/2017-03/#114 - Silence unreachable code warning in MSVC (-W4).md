# #114 Silence unreachable code warning in MSVC (/W4) [Closed]

> Username: norbertwenzel  
> Created at: 2017-03-08 19:35:43 UTC  
> Updated at: 2020-01-07 06:45:32 UTC  
> Closed at: 2020-01-07 06:45:32 UTC  
> Url: https://github.com/boostorg/test/pull/114  

Disable level 4 warning C4702 on MSVC. Since the comments indicate, that the unreachable `return 0` silences some other compiler warning I think it is best to simply ignore the MSVC warning.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2017-05-23 06:51:37 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-303306578  

Hi, have you considered using `BOOST_UNREACHABLE_RETURN` ? (see [there](http://www.boost.org/doc/libs/1_64_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.boost_helper_macros)) If not, would you please give a try?

---

## Comment 2

> Username: norbertwenzel  
> Created_at: 2017-06-06 20:27:22 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-306606849  

I did try `BOOST_UNREACHABLE_RETURN` and it works for Release builds only. It seems that MSVC Debug builds require these return statements they warn about in Release builds.  
  
I've tested VS2017 (15.2) with Boost 1.64 which does *not* trigger a warning in `execution_monitor.ipp` as I intended to fix. But it triggers the same warning in a different spot, `util/named_params.hpp:138`, which also can be fixed using `BOOST_UNREACHABLE_RETURN` only in Release builds. Debug builds cause level 1 [compiler warning C4716](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-1-c4716), which is by default treated as a build error.  
  
I've also tested VS2015 (14.x) with Boost 1.63, which led to this PR and, if I remember correctly (I don't have that computer available at the moment), produced the exact same problems as described above. The macro works in Release builds only and causes actual compile errors in Debug.  
  
In Release this macro works fine and is definitely a superior solution to my `pragma push/pop` PR.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2017-06-06 20:43:21 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-306611079  

What about this: provide me with the commit, and I'll try to run it on other platforms. If there is something wrong with the macro, then I'll report to the boost ML.

---

## Comment 4

> Username: norbertwenzel  
> Created_at: 2017-06-07 17:07:02 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-306860920  

You mean the commit using the macro? Sure. Should I just update the one I fixed in the initial PR or should I also take care of the ones in `named_params.hpp`?

---

## Comment 5

> Username: norbertwenzel  
> Created_at: 2017-06-08 19:18:00 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-307200987  

I've added both changes in different commits. so you can separate them if necessary.   
The code is tested and compiles flawlessly in MSVC 2017 at /W4 in Release builds. **Debug builds fail as described before.**

---

## Comment 6

> Username: norbertwenzel  
> Created_at: 2017-06-09 13:30:50 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-307389046  

I've also tested with MVSC 2015 now. The behaviour is the same as in 2017. Release build is fine, but Debug build fails.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2018-02-04 18:42:49 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-362929335  

On branch ``topic/PR114-unreachable-code-warning-MSVC``, tests running

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-11-11 14:56:56 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-437676894  

Perhaps this never got merged.  Is it the same issue as #180 ?

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2018-11-11 15:03:56 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-437677634  

Yes, the PR as proposed causes build issues and I never got the time to rework it.

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2019-11-11 21:02:03 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-552614217  

I am not sure why you were seeing warnings, I believe the `BOOST_UNREACHABLE_RETURN` in `execution_monitor.ipp` is legit, but the ones in `named_params.hpp` are not. I am keeping the one in `execution_monitor.ipp` to finally close this PR.

---

## Comment 11

> Username: raffienficiaud  
> Created_at: 2020-01-07 06:45:32 UTC  
> Url: https://github.com/boostorg/test/pull/114#issuecomment-571458913  

Merged to master

---
