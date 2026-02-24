# #30 Fix Boost_USE_MULTITHREADED & Boost_USE_DEBUG_RUNTIME logic error [Closed]

> Username: Bo98  
> Created at: 2020-02-28 01:29:28 UTC  
> Updated at: 2020-02-29 12:29:47 UTC  
> Closed at: 2020-02-28 04:01:38 UTC  
> Url: https://github.com/boostorg/boost_install/pull/30  

If Boost_USE_MULTITHREADED or Boost_USE_DEBUG_RUNTIME are not defined, they are currently evaluated by Boost's scripts as if they are _neither_ ON and OFF. This commit fixes it so it correctly defaults to ON.  
  
To explain more fully, before this change the checks were as follows:  
  
* If Boost_USE_MULTITHREADED is defined (not empty string) AND is evaluates to false/OFF:  
  - skip multithreaded.  
* If Boost_USE_MULTITHREADED evaluates to true/ON:  
  - skip single threaded  
  
However if Boost_USE_MULTITHREADED is not defined, neither condition is satisfied so neither is skipped. It doesn't satisify the first part of the first condition and it doesn't pass the second condition because a not defined variable isn't truthy.  
  
The checks could have also been done by using `DEFINED` and `NOT DEFINED` but I kept it as `STREQUAL` for consistency with the rest of the script.

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-02-28 02:43:08 UTC  
> Url: https://github.com/boostorg/boost_install/pull/30#issuecomment-592283360  

The logic is as it is by design, not by mistake, and in the Boost_USE_DEBUG_RUNTIME case your change actively breaks things, so I'd leave that alone.  
  
For Boost_USE_MULTITHREADED, one might legitimately prefer the new behavior. The case that would be affected negatively would be when only the single-threaded variant is installed and Boost_USE_MULTITHREADED is not set. This works now, would not work after the change. However, on balance, the change may still be worth it, because I think this scenario might be rarer than the case when both are installed.  
  
Not sure how we can ascertain that this isn't being used though. We could just try the usual method of going ahead with the change for 1.73 and seeing if anyone complains.

---

## Comment 2

> Username: Bo98  
> Created_at: 2020-02-28 03:58:07 UTC  
> Updated_at: 2020-02-28 03:59:58 UTC  
> Url: https://github.com/boostorg/boost_install/pull/30#issuecomment-592301467  

> single-threaded variant is installed and Boost_USE_MULTITHREADED is not set  
  
That's a fair catch. I missed that one - apologies for being careless and wasting your time here.  
  
I suppose the main issue is how confusing the current situation is. For example, if you compile [azure-storage-cpp](https://github.com/Azure/azure-storage-cpp) (just an example - there's nothing particularly specific to this project) in a mixed ST/MT environment, it will fail at link time with cryptic errors. In the current scenario, a mixture of ST & MT libraries compile against each other. This leads to [some headscratching](https://github.com/robotastic/trunk-recorder/issues/277#issuecomment-564248162) (example comment in a bug report from another project). There has also been bug reports submitted to CMake which have ultimately lead to people recommending to bypass Boost's CMake files (`Boost_NO_BOOST_CMAKE`).  
  
Of course though, it would be disappointing to then break ST-only environments, though I suppose at least it breaks at configure time, with more obvious errors. I get the sense that it's not trivial under the current system to address all cases, so I'd probably be wasting my time figuring out how everything here is currently pieced together.  
  
What I can take away from this is that I at least know what the problem is now, so I can submit patches now where relevant to start using `-DBoost_USE_MULTITHREADED=ON` always. For example, a few packages in Homebrew were unable to be updated because of errors with Boost libraries and no one until now knew what the issue was.  
  
If there's an official stance on ST & MT environments, I can try follow that. Since 2013, Homebrew has supplied both ST & MT to give full flexibility on which can be used, but if that's not a supported environment then a change to that can be proposed.

---

## Comment 3

> Username: pdimov  
> Created_at: 2020-02-28 12:11:10 UTC  
> Url: https://github.com/boostorg/boost_install/pull/30#issuecomment-592486703  

I appreciate your bringing up the issue; it's just that the fix is not that straightforward.

---

## Comment 4

> Username: pdimov  
> Created_at: 2020-02-28 13:51:23 UTC  
> Url: https://github.com/boostorg/boost_install/pull/30#issuecomment-592521430  

In hindsight, I should probably have made the warning  
```  
  Target Boost::filesystem already has an imported location  
  '/home/travis/.local/lib/libboost_filesystem-mt.so.1.73.0', which will be  
  overwritten with '/home/travis/.local/lib/libboost_filesystem.so.1.73.0'  
```  
an error.

---

## Comment 5

> Username: Bo98  
> Created_at: 2020-02-28 14:08:45 UTC  
> Url: https://github.com/boostorg/boost_install/pull/30#issuecomment-592528475  

> it's just that the fix is not that straightforward.  
  
I appreciate that, and didn't mean to imply any other way.

---

## Comment 6

> Username: pdimov  
> Created_at: 2020-02-29 12:29:47 UTC  
> Url: https://github.com/boostorg/boost_install/pull/30#issuecomment-592940495  

I implemented your suggestion on develop. Boost_USE_MULTITHREADED now defaults to ON. To avoid the regression I look for `--layout=system`, in which case it's assumed that only one variant is present and the check is skipped.

---
