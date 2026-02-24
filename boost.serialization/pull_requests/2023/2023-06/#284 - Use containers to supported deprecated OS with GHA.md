# #284 Use containers to supported deprecated OS with GHA [Merged]

> Username: mborland  
> Created at: 2023-06-28 06:24:46 UTC  
> Updated at: 2023-08-23 19:18:14 UTC  
> Merged at: 2023-08-23 18:53:50 UTC  
> Closed at: 2023-08-23 18:53:50 UTC  
> Url: https://github.com/boostorg/serialization/pull/284  

Github deprecated support for a number of old Ubuntu distributions so use containers and the Ubuntu toolchain PPA to keep support for older compilers.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-07-07 12:56:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/284#issuecomment-1625377358  

@robertramey This is good for review. It has the Github Actions CI system working again, and a few minor fixes to that end. If you want you can go into the repo settings and delete the webhook for appveyor since that doesn't seem to work. That'll give you the green check mark next to PRs/commits moving forward.

---
