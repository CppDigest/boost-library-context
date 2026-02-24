# #671 Fixed io extension build error [Merged]

> Username: simmplecoder  
> Created at: 2022-05-13 11:45:15 UTC  
> Updated at: 2022-05-13 16:46:05 UTC  
> Merged at: 2022-05-13 12:13:00 UTC  
> Closed at: 2022-05-13 12:13:00 UTC  
> Url: https://github.com/boostorg/gil/pull/671  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
The error was due to missing dynamic.hpp header that contained dynamic_io_fnobj. Adding the header back in to write.hpp headers of io extensions fixed the problem.  
### References  
  
[#653 (comment)](https://github.com/boostorg/gil/pull/653#issuecomment-1125804514).  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
<s>Add test case(s)</s> N/A (run build and tests locally, all passed)  
- [ ] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: sdebionne  
> Created_at: 2022-05-13 12:02:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/671#pullrequestreview-972152485  

SGTM thanks! It's strange that the CI did not catch the issue.

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-05-13 12:11:12 UTC  
> Url: https://github.com/boostorg/gil/pull/671#issuecomment-1125990719  

> It's strange that the CI did not catch the issue.  
  
And, I believe I did run local build too! Mystery :)

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2022-05-13 12:11:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/671#pullrequestreview-972161288  

Thanks !

---

## Comment 4

> Username: marco-langer  
> Created_at: 2022-05-13 16:46:05 UTC  
> Url: https://github.com/boostorg/gil/pull/671#issuecomment-1126251657  

thanks for the fix on my PR!  
  
I'll join the club of surprised people that the bug was not caught in my own test run :-)

---
