# #77 Optimize includes [Closed]

> Username: Kojoley  
> Created at: 2019-12-01 22:04:57 UTC  
> Updated at: 2023-12-29 11:26:39 UTC  
> Closed at: 2023-12-29 11:26:39 UTC  
> Url: https://github.com/boostorg/optional/pull/77  

Swap from Boost.Move includes less headers, giving 6-12% speed-up.  
  
```  
clang-cl 0.48 -> 0.45 (6%)  
mingw    0.43 -> 0.38 (12%)  
```

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2023-10-01 09:48:16 UTC  
> Url: https://github.com/boostorg/optional/pull/77#issuecomment-1742025473  

Is this PR still relevant after https://github.com/boostorg/optional/pull/110 has been applied?

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2023-12-29 11:26:39 UTC  
> Url: https://github.com/boostorg/optional/pull/77#issuecomment-1871969588  

Closing this as fixed by https://github.com/boostorg/optional/pull/110.

---
