# #267 - Windows namespace prefixes [Closed]

> Username: asfernandes  
> Created at: 2025-07-29 01:09:51 UTC  
> Updated at: 2026-01-07 08:39:50 UTC  
> Closed at: 2026-01-07 08:39:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/267  

Windows kernel objects may be created with prefixes like `Global\`, `Local\` or even custom namespaces.  
  
Can boost.interprocess get rid of fixed prefixes and let developers make names in the way they want for advanced usage?

---

## Comment 1

> Username: asfernandes  
> Created at: 2025-07-30 11:19:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/267#issuecomment-3135862330  

Would a PR in this regard (also allowing direct usage of user-passed names) be accepted?

---

## Comment 2

> Username: asfernandes  
> Created at: 2025-08-05 22:34:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/267#issuecomment-3156834336  

Is this project still maintained?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2025-08-06 09:05:34 UTC  
> Url: https://github.com/boostorg/interprocess/issues/267#issuecomment-3158448722  

Thanks! A PR is welcome, we need to keep the backwards compatibility, please make a proposal.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2026-01-07 08:39:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/267#issuecomment-3717844574  

Closing this due to inactivitiy, please submit a pull request if the feature is still interesting.
