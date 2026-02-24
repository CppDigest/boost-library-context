# #43 comment out unused variable. [Closed]

> Username: MarisaKirisame  
> Created at: 2015-04-14 15:57:50 UTC  
> Updated at: 2015-04-14 23:21:36 UTC  
> Closed at: 2015-04-14 23:21:36 UTC  
> Url: https://github.com/boostorg/hana/pull/43  



---

## Comment 1

> Username: ldionne  
> Created_at: 2015-04-14 18:20:32 UTC  
> Url: https://github.com/boostorg/hana/pull/43#issuecomment-93006803  

First, thanks for contributing! I've been using the `-Wno-unused-parameter` flag since the beginning of the project, and I did not realize it would show up as warnings in user code. That's a good catch. Your PR also made me realize I had a bug in the constexpr algorithms, which is now fixed by 06126249.  
  
I made some tweaks:  
- Removed the `-Wno-unused-parameter` flag from CMake  
- Removed more unused parameter warnings from the tests  
- Rebased on top of develop  
  
The PR is currently being tested on Travis (846cfe9) to make sure there are no more unused parameter warnings, and once that's done I'll merge.  
  
Thanks!

---
