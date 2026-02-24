# #48 MSVC "assignment within conditional" warning fix [Merged]

> Username: poelmanc  
> Created at: 2020-01-25 21:07:53 UTC  
> Updated at: 2020-10-19 11:32:35 UTC  
> Merged at: 2020-10-19 11:32:22 UTC  
> Closed at: 2020-10-19 11:32:22 UTC  
> Url: https://github.com/boostorg/intrusive/pull/48  

The latest MSVC 2019 issues a warning on this line. This change performs the assignment prior to the conditional.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2020-10-19 11:32:35 UTC  
> Url: https://github.com/boostorg/intrusive/pull/48#issuecomment-712095420  

Thanks for the patch!

---
