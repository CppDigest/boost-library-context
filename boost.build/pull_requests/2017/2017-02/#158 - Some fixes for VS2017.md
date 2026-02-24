# #158 Some fixes for VS2017 [Merged]

> Username: KindDragon  
> Created at: 2017-02-01 19:17:56 UTC  
> Updated at: 2021-10-02 22:18:56 UTC  
> Merged at: 2017-02-02 13:00:01 UTC  
> Closed at: 2017-02-02 13:00:01 UTC  
> Url: https://github.com/boostorg/build/pull/158  

Path autodetection to VS works only if it installed to default path

---

## Comment 1

> Username: KindDragon  
> Created_at: 2017-02-01 19:23:59 UTC  
> Url: https://github.com/boostorg/build/pull/158#issuecomment-276754528  

Is it acceptable to call this PowerShell snippet http://stackoverflow.com/a/41968736/61505 from Python?  
Also, we can create a small C++ tool that will return path to VS2017 and distribute it binary with Boost.Build.  
IDK how to do that in plain Python

---

## Review 2 [Commented]

> Username: grafikrobot  
> Created_at: 2017-02-01 19:32:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/158#pullrequestreview-19634867  

📁 src/engine/build.bat

```diff
 472 |     )
 463 |- if "_%VCINSTALLDIR%_" == "__" call :Call_If_Exists "%BOOST_JAM_TOOLSET_ROOT%VCVARSALL.BAT" %BOOST_JAM_ARGS%
 473 |+ if "_%VCINSTALLDIR%_" == "__" call :Call_If_Exists "%BOOST_JAM_TOOLSET_ROOT%Auxiliary\Build\vcvarsall.bat" %BOOST_JAM_ARGS%
```

> Username: grafikrobot  
> Created_at: 2017-02-01 19:32:25 UTC  
> Url: https://github.com/boostorg/build/pull/158#discussion_r98975452  

Won't that break with earlier VS versions as they don't have the "Auxiliary\Build" path?

> Username: KindDragon  
> Created_at: 2017-02-01 19:46:49 UTC  
> Updated_at: 2017-02-01 19:46:50 UTC  
> Url: https://github.com/boostorg/build/pull/158#discussion_r98978716  

I thought that the line https://github.com/KindDragon/build/blob/cb70dbeb5cadfe94b48ad0d97452811cc4e5bbfc/src/engine/build.bat#L458  for this

> Username: grafikrobot  
> Created_at: 2017-02-01 19:52:06 UTC  
> Url: https://github.com/boostorg/build/pull/158#discussion_r98979845  

Ah, right.. Didn't realize the context. NM then.


---

## Comment 3

> Username: KindDragon  
> Created_at: 2017-02-02 15:08:29 UTC  
> Url: https://github.com/boostorg/build/pull/158#issuecomment-276982868  

@grafikrobot @frenchtoast747 @vprus What do you think about my question? Now it is impossible to get the path to VS2017 without access to the VS installer COM object.

---
