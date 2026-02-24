# #178 Add test for \e \E escape sequences overlooked in prior commit [Merged]

> Username: jefftrull  
> Created at: 2022-11-09 02:15:47 UTC  
> Updated at: 2022-11-09 08:02:20 UTC  
> Merged at: 2022-11-09 08:02:19 UTC  
> Closed at: 2022-11-09 08:02:20 UTC  
> Url: https://github.com/boostorg/wave/pull/178  

I somehow managed to miss this in my last commit. This test verifies that \e and \E can be used in preprocessor conditional expressions.

---
