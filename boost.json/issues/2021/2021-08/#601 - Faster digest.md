# #601 - Faster digest [Open]

> Username: vinniefalco  
> Created at: 2021-08-04 17:56:27 UTC  
> Updated at: 2021-08-08 14:31:12 UTC  
> Url: https://github.com/boostorg/json/issues/601  

When calculating digests of strings, we could operate 4 or 8 characters at a time. First we need to run the profile to see what the current digest algorithm costs us during parsing.

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-08-08 14:24:59 UTC  
> Updated at: 2021-08-08 14:31:12 UTC  
> Url: https://github.com/boostorg/json/issues/601#issuecomment-894805662  

Here's my benchmarking based on 4 approaches from PR #606 (each commit is a different approach).  
https://disk.yandex.ru/i/7tKxDT2YG3o-Yw
