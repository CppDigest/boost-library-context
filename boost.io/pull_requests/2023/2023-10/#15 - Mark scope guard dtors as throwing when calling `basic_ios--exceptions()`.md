# #15 Mark scope guard dtors as throwing when calling `basic_ios::exceptions()` [Merged]

> Username: Lastique  
> Created at: 2023-10-01 20:33:58 UTC  
> Updated at: 2023-10-02 21:22:23 UTC  
> Merged at: 2023-10-02 21:15:25 UTC  
> Closed at: 2023-10-02 21:15:25 UTC  
> Url: https://github.com/boostorg/io/pull/15  

`basic_ios::exceptions()` may immediately throw an exception, if the stream has an error condition that matches the newly set exception mask. Since it is called in destructors of some scope guards, these destructors have to be explicitly marked as throwing to avoid terminating the process.

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-10-02 21:22:22 UTC  
> Url: https://github.com/boostorg/io/pull/15#issuecomment-1743786258  

@glenfe Please, remember to merge to master. My previous PR (https://github.com/boostorg/io/pull/14) was merged more than a year ago and still not in master.

---
