# #93 Small GHA fix, create destination dir [Merged]

> Username: sdarwin  
> Created at: 2021-07-27 18:46:21 UTC  
> Updated at: 2021-07-27 20:53:56 UTC  
> Merged at: 2021-07-27 20:53:45 UTC  
> Closed at: 2021-07-27 20:53:45 UTC  
> Url: https://github.com/boostorg/core/pull/93  

Earlier, I copied ci.yml from boostorg/core to boostorg/boost-ci where it can be used as a template when setting up new boost libraries.  Discovered a problem - if a new library doesn't yet exist in boost, the lib directory won't exist, and a copy will fail.   So, create the directory first.  It would be convenient to backport the change upstream, here.  Or even to any repo that might later be used as an example.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-07-27 18:56:15 UTC  
> Url: https://github.com/boostorg/core/pull/93#issuecomment-887755396  

Is a similar fix needed for Windows?

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-07-27 19:02:14 UTC  
> Url: https://github.com/boostorg/core/pull/93#issuecomment-887759412  

No, the backslash at the end of `xcopy /s /e /q %GITHUB_WORKSPACE% libs\%LIBRARY%\` creates the directory if it doesn't exist.

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-07-27 20:53:56 UTC  
> Url: https://github.com/boostorg/core/pull/93#issuecomment-887827521  

Thanks.

---
