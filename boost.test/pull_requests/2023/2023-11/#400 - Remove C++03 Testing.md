# #400 Remove C++03 Testing [Merged]

> Username: mborland  
> Created at: 2023-11-14 08:01:21 UTC  
> Updated at: 2023-11-15 14:20:43 UTC  
> Merged at: 2023-11-15 14:20:39 UTC  
> Closed at: 2023-11-15 14:20:39 UTC  
> Url: https://github.com/boostorg/test/pull/400  



---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2023-11-14 08:11:49 UTC  
> Url: https://github.com/boostorg/test/pull/400#issuecomment-1809720253  

Hi Matt, if I am not mistaken there is a announcement to make on Boost mailing list so that Boost.Test can drop support of C++03. Maybe this has been done already, I have not checked.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-11-14 08:15:14 UTC  
> Url: https://github.com/boostorg/test/pull/400#issuecomment-1809724343  

> Hi Matt, if I am not mistaken there is a announcement to make on Boost mailing list so that Boost.Test can drop support of C++03. Maybe this has been done already, I have not checked.  
  
You are correct. I noticed on this PR's CI run that a number of the upstream libraries removed C++03 support: https://github.com/boostorg/test/pull/399. I'll add a warning in the release that is happening now that in the next release C++03 will be gone.

---
