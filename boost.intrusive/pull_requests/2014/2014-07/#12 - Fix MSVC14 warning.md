# #12 Fix MSVC14 warning [Closed]

> Username: MarcelRaad  
> Created at: 2014-07-03 16:43:17 UTC  
> Updated at: 2014-08-01 12:07:38 UTC  
> Closed at: 2014-08-01 09:10:38 UTC  
> Url: https://github.com/boostorg/intrusive/pull/12  

This fixes  
"warning C4456: declaration of 'x_parent_right' hides previous local declaration".

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-08-01 09:10:38 UTC  
> Url: https://github.com/boostorg/intrusive/pull/12#issuecomment-50864330  

Thanks for the report. Change applied and loop tidied in commit:  
  
[develop a6a65cf] GitHub #12: Fix MSVC14 warning C4456: declaration of 'x_parent_right' hides previous local declaration

---
