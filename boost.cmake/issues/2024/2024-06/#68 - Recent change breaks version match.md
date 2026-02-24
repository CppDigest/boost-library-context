# #68 - Recent change breaks version match [Closed]

> Username: soro  
> Created at: 2024-06-20 14:52:39 UTC  
> Updated at: 2024-06-20 16:38:42 UTC  
> Closed at: 2024-06-20 16:38:42 UTC  
> Url: https://github.com/boostorg/cmake/issues/68  

It seems that this change breaks version matching:  
[440dd10f57f86a91c9d0ae23f096f343fdd004cb](https://github.com/boostorg/cmake/commit/440dd10f57f86a91c9d0ae23f096f343fdd004cb)  
  
```  
  Could not find a configuration file for package "boost_context" that  
  exactly matches requested version "1.85.0".  
  
  The following configuration files were considered but not accepted:  
  
    /home/dev/.cache/bazel/_bazel_dev/15902ddbe5afade2111503033c667920/sandbox/processwrapper-sandbox/7/execroot/cpp/bazel-out/k8-fastbuild/bin/external/com_github_facebook_folly/facebook_folly.ext_build_deps/boost/lib/cmake/boost_context-1.85.0-static/boost_context-config.cmake, version: 1.85.0 (static)  
```  
  
The suffix means it's not an exact match. I doubt that was the intended effect?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-06-20 15:03:56 UTC  
> Url: https://github.com/boostorg/cmake/issues/68#issuecomment-2180931512  

It's not the suffix; my guess is that you have `Boost_USE_STATIC_LIBS` set to `OFF`.

---

## Comment 2

> Username: soro  
> Created at: 2024-06-20 15:19:02 UTC  
> Url: https://github.com/boostorg/cmake/issues/68#issuecomment-2180962518  

I have it set to ON, hence the assumption that it has something to do with the suffix - at the 1.85.0 release tag the names didn't yet have the suffix iirc and the change to add the HINT actually allowed it to build.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-06-20 15:23:00 UTC  
> Url: https://github.com/boostorg/cmake/issues/68#issuecomment-2180970799  

As you can see here  
  
https://github.com/boostorg/cmake/blob/5eab56dd6cf2e96d7194525839fee3e5b30ec4c1/include/BoostInstall.cmake#L489-L494  
  
the "(static)" suffix is only added to the version when `DEFINED Boost_USE_STATIC_LIBS AND NOT Boost_USE_STATIC_LIBS`.

---

## Comment 4

> Username: pdimov  
> Created at: 2024-06-20 15:24:44 UTC  
> Url: https://github.com/boostorg/cmake/issues/68#issuecomment-2180974317  

Although I'm not sure why the version is 1.85.0, because the static/shared change has been added after 1.85 was released. So the version should be 1.86.0 when using a current develop or master.

---

## Comment 5

> Username: soro  
> Created at: 2024-06-20 15:34:09 UTC  
> Url: https://github.com/boostorg/cmake/issues/68#issuecomment-2180994793  

I just updated the `cmake` submodule, not the entire tree. Maybe that was the problem? I have reverted to the 1.85.0 version and applied only the hint change and it builds again. Sorry, I'm currently doing this as a side project to get rid of our old way of managing some third party dependencies and have limited time to dig into it. Trying to be as helpful as I can :)

---

## Comment 6

> Username: pdimov  
> Created at: 2024-06-20 15:40:47 UTC  
> Url: https://github.com/boostorg/cmake/issues/68#issuecomment-2181008042  

Updating just the cmake module should have worked; at this point I have no idea where the problem might be. As I said above, the version is only appended " (static)" so that it can appear in the error message, and this only happens when Boost_USE_STATIC_LIBS is OFF.

---

## Comment 7

> Username: soro  
> Created at: 2024-06-20 16:38:42 UTC  
> Url: https://github.com/boostorg/cmake/issues/68#issuecomment-2181113985  

I guess I'll close this for now - maybe it was a caching issue. Will let you know once I try to update to next release
