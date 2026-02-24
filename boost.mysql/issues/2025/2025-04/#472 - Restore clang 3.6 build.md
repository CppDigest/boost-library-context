# #472 - Restore clang 3.6 build [Closed]

> Username: anarthal  
> Created at: 2025-04-25 08:13:12 UTC  
> Updated at: 2025-10-07 12:41:06 UTC  
> Closed at: 2025-10-07 10:19:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/472  

B2 seems to not build with clang 3.6. I've commented out the build for now.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-07 10:19:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/472#issuecomment-3376189507  

B2 no longer builds at all with clang < 4. I doubt this old compiler is useful to anyone, so I will raise the minimum requirement.
