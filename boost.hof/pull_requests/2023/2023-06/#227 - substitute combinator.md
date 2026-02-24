# #227 substitute combinator [Closed]

> Username: ryanelandt  
> Created at: 2023-06-19 16:51:22 UTC  
> Updated at: 2023-06-19 20:58:04 UTC  
> Closed at: 2023-06-19 19:59:26 UTC  
> Url: https://github.com/boostorg/hof/pull/227  

I added the substitute combinator as described in #192.   
  
The implementation is nearly identical to the implementation of compose/flow. I tried a few times to have them share code, but I ran into various template parameter problems.  
  
Lines 61 and 74 of the `substitute.hpp` file are commented out. They don't appear to be necessary, but are present in the compose/flow files. I think I should either leave them it, or remove them from all the files. I'm not sure which.

---

## Comment 1

> Username: ryanelandt  
> Created_at: 2023-06-19 19:59:26 UTC  
> Updated_at: 2023-06-19 20:58:04 UTC  
> Url: https://github.com/boostorg/hof/pull/227#issuecomment-1597692942  

This PR does not compile on the Windows Visual Studio according to the CI. I was able to track down some of these issues by testing more compilers on Ubuntu and checking for warnings, but other issues remain. I don't have a good way of debugging these problems, so I'm closing this PR.

---
