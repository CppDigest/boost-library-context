# #106 Fix tests [Merged]

> Username: res2k  
> Created at: 2018-04-30 23:39:00 UTC  
> Updated at: 2018-05-01 02:14:29 UTC  
> Merged at: 2018-05-01 02:14:29 UTC  
> Closed at: 2018-05-01 02:14:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/106  

8ca532a changed some bjam rule signatures in `util/test.jam`, but not all invocations of the changed rules were updated, systematically breaking test builds.  
  
Also, the "test_dll_plugin" test was re-activated. However, as it is now it can't work - the `.so`/`.dll` name used doesn't match what's built.  
  
Both of these issues are fixed by this PR.

---
