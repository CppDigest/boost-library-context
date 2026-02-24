# #111 arm64 drone tests [Closed]

> Username: sdarwin  
> Created at: 2022-01-13 20:15:40 UTC  
> Updated at: 2022-03-12 03:08:41 UTC  
> Closed at: 2022-03-12 03:08:41 UTC  
> Url: https://github.com/boostorg/url/pull/111  

In order to test on arm64 architecture, place `arch="arm64"` in the job specification and use the new multiarch docker images:  
  
```  
image="cppalliance/droneubuntu2004:multiarch"  
```  
  
```  
image="cppalliance/droneubuntu1804:multiarch"  
```  
  
These will run on AWS Graviton2 instances.    
  
Testing the arm architecture is interesting, the general level of support by software vendors seems to be at the 80% level instead of 100%. Certain upstream packages are randomly broken such as clang-11 llvm, but then clang-9,10,12 are fine.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-01-19 19:36:48 UTC  
> Url: https://github.com/boostorg/url/pull/111#issuecomment-1016803395  

Should I merge this or is it going to create failures?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2022-01-19 19:47:24 UTC  
> Url: https://github.com/boostorg/url/pull/111#issuecomment-1016811160  

From the perspective of Drone, this is ready to merge.  arm64 builds are functional.    
  
From the perspective of the Boost.URL codebase, when these tests ran 6 days ago they had failures.  
  
So, to avoid any ongoing CI failure mode, confirm arm64 fixes in your own fork before merging.  @alandefreitas @vinniefalco

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2022-02-28 19:52:13 UTC  
> Updated_at: 2022-02-28 20:19:14 UTC  
> Url: https://github.com/boostorg/url/pull/111#issuecomment-1054606422  

I rebased it [here](https://github.com/alandefreitas/url/tree/feature/arm64) and merged `drone.star`, which has changed a lot since this PR was opened.   
  
So the problem here was arm64 char is unsigned. This is [working now](https://drone.cpp.al/alandefreitas/url/59/1/2) since we fixed this problem in PR #116.  
  
On a side note, GHA was triggered [here](https://github.com/alandefreitas/url/actions/runs/1912244019) as soon as I pushed this branch [`feature/arm64`](https://github.com/alandefreitas/url/tree/feature/arm64), but I don't see the drone tests that failed. Do I need to open an internal PR to trigger these?

---

## Comment 4

> Username: sdarwin  
> Created_at: 2022-02-28 20:09:13 UTC  
> Url: https://github.com/boostorg/url/pull/111#issuecomment-1054619387  

> I don't see the drone tests that failed.   
  
Your repo's branch feature/arm64 is not the same as this pull request. Your new test results appear here https://drone.cpp.al/alandefreitas/url/59 and the tests pass.

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-02-28 20:20:41 UTC  
> Url: https://github.com/boostorg/url/pull/111#issuecomment-1054628909  

> Your repo's branch feature/arm64 is not the same as this pull request.   
  
Yes. I understand that.  
  
> Your new test results appear here https://drone.cpp.al/alandefreitas/url/59 and the tests pass.  
  
Mmm... That's what I didn't know. If this was going to be triggered automatically. Great!  
  
So this is rebased and working: https://github.com/alandefreitas/url/tree/feature/arm64

---
