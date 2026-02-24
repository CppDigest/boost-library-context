# #824 Fix: unsigned long is not fundamental type on Win64 [Merged]

> Username: jszuppe  
> Created at: 2019-02-11 12:21:37 UTC  
> Updated at: 2019-02-11 14:30:38 UTC  
> Merged at: 2019-02-11 14:30:38 UTC  
> Closed at: 2019-02-11 14:30:38 UTC  
> Url: https://github.com/boostorg/compute/pull/824  

On Windows 64 unsigned long has 4 bytes like unsigned int, but it does not seem to be a simple alias for unsigned int. Because of that unsigned long is not OpenCL fundamental type.

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-02-11 14:25:29 UTC  
> Url: https://github.com/boostorg/compute/pull/824#issuecomment-462345477  

[![Coverage Status](https://coveralls.io/builds/21568874/badge)](https://coveralls.io/builds/21568874)  
  
Coverage remained the same at 84.019% when pulling **590c814bbe245ac03acef49675148f7f9ee6f406 on jszuppe:fix-ulong-type** into **a47ff938b78e30362ec8abd466b5a2773d4aae8d on boostorg:develop**.

---
