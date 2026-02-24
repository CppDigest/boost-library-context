# #325  Implements the writer as an FSM and adds tests [Merged]

> Username: anarthal  
> Created at: 2025-10-03 19:12:06 UTC  
> Updated at: 2025-10-09 09:31:40 UTC  
> Merged at: 2025-10-09 09:31:36 UTC  
> Closed at: 2025-10-09 09:31:36 UTC  
> Url: https://github.com/boostorg/redis/pull/325  

* Refactors the writer task into a FSM and adds unit tests.  
* Adds a testing utility to check logging.  
  
Entails no functional change (other than cosmetic word fixes to the logs).  
  
Supersedes #292, which had some logging refactors that will go into another PR.

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-03 19:12:22 UTC  
> Url: https://github.com/boostorg/redis/pull/325#issuecomment-3366902242  

Still an early prototype.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-10-07 17:24:25 UTC  
> Updated_at: 2025-10-07 17:24:47 UTC  
> Url: https://github.com/boostorg/redis/pull/325#issuecomment-3377843588  

I've rebased this on top of develop, but not squashed it (all commits shown here belong to the feature). Could you please confirm that you can see the whole diff without getting too crazy (e.g. https://github.com/boostorg/redis/pull/325/files)?

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-10-09 09:30:52 UTC  
> Url: https://github.com/boostorg/redis/pull/325#issuecomment-3384984474  

Merging as requested by Marcelo.

---
