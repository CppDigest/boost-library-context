# #144 Added out of range exception for static tensor's [Closed]

> Username: shikharvashistha  
> Created at: 2021-11-05 08:45:05 UTC  
> Updated at: 2023-12-05 15:15:32 UTC  
> Closed at: 2023-12-05 15:15:31 UTC  
> Url: https://github.com/boostorg/ublas/pull/144  

This pulls adds out of range exception for static tensors  
  
Fixes : #117

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-11-05 09:24:30 UTC  
> Url: https://github.com/boostorg/ublas/pull/144#issuecomment-961742654  

This Pull request Passed all of clang-tidy tests. :+1:

---

## Comment 2

> Username: amitsingh19975  
> Created_at: 2022-02-07 12:59:25 UTC  
> Updated_at: 2022-02-07 12:59:52 UTC  
> Url: https://github.com/boostorg/ublas/pull/144#issuecomment-1031441565  

I think you misconstrued the issue. The arity of the function is already checked using static assert and on top of that, you added the same check during runtime. The issue was to fix the out of bound access, for example:  
  
```cpp  
int a[20];  
a[0] = 3; // valid access  
a[23] = 5; // invalid access or accessing unowned memory, which is the out of bound access.  
```

---
