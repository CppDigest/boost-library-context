# #244 Implement support for digit separators in preprocessor expressions [Merged]

> Username: jefftrull  
> Created at: 2025-11-13 22:36:22 UTC  
> Updated at: 2025-11-17 01:23:04 UTC  
> Merged at: 2025-11-17 01:23:04 UTC  
> Closed at: 2025-11-17 01:23:04 UTC  
> Url: https://github.com/boostorg/wave/pull/244  

Integers with digit separators are already recognized as literal tokens. This commit adds proper interpretation of them as integers for use in expressions, e.g. with `#if`, by:  
  
1. Adding digit separators to the RE2C code that handles pp-numbers,  
2. Filtering out digit separators before extracting numeric values, and  
3. Adding appropriate unit tests

---

## Comment 1

> Username: jefftrull  
> Created_at: 2025-11-13 22:37:46 UTC  
> Url: https://github.com/boostorg/wave/pull/244#issuecomment-3530010391  

FYI @APokorny

---
