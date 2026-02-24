# #254 Add fast-path for lower case ASCII letters in case_fold [Merged]

> Username: andreasbuhr  
> Created at: 2025-08-02 12:12:51 UTC  
> Updated at: 2025-10-13 02:15:37 UTC  
> Merged at: 2025-10-13 02:15:37 UTC  
> Closed at: 2025-10-13 02:15:37 UTC  
> Url: https://github.com/boostorg/parser/pull/254  

This improved the speed of my parser by approximately 20%  
  
There is a fast-path in case_fold for uppercase ASCII letters to convert them to lowercase ASCII letters. But there is not fast-path to keep lowercase ASCII letters. Am I missing something here?

---

## Comment 1

> Username: andreasbuhr  
> Created_at: 2025-08-02 12:16:01 UTC  
> Url: https://github.com/boostorg/parser/pull/254#issuecomment-3146468386  

Driveby bugfix: The upper bound should be compared with "<=", not "<", because 0x5A is "Z" and 0x7A is "z".

---

## Comment 2

> Username: tzlaine  
> Created_at: 2025-10-13 02:15:33 UTC  
> Url: https://github.com/boostorg/parser/pull/254#issuecomment-3395652140  

Thanks!

---
