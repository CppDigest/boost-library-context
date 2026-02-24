# #226 - Fuzzers crashing in develop [Closed]

> Username: anarthal  
> Created at: 2024-03-14 10:22:20 UTC  
> Updated at: 2024-03-15 18:31:41 UTC  
> Closed at: 2024-03-15 18:31:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/226  

When a fuzzer crashes, it's not picking the offending sample correctly. CI problem.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-03-14 10:53:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/226#issuecomment-1997164742  

Update: it looks like no crash file is being generated. Fuzzers are still crashing, so an investigation is required.
