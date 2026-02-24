# #274 Write GDB pretty-printers for all containers and iterators [Merged]

> Username: k3DW  
> Created at: 2024-08-17 16:13:49 UTC  
> Updated at: 2024-08-23 04:49:51 UTC  
> Merged at: 2024-08-23 04:45:23 UTC  
> Closed at: 2024-08-23 04:45:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/274  

Closes #162   
  
This will cause a merge conflict with #271. I will fix it, regardless of which of the pull requests get merged first.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-08-17 16:22:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2294904724  

An automated preview of the documentation is available at [https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-08-17 16:42:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2294909165  

An automated preview of the documentation is available at [https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-08-17 17:57:51 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2294927963  

An automated preview of the documentation is available at [https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 4

> Username: k3DW  
> Created_at: 2024-08-17 19:38:48 UTC  
> Updated_at: 2024-08-18 15:49:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2294956221  

The builds are getting some errors that are unrelated to Unordered. See [boostorg/uuid#162](https://github.com/boostorg/uuid/issues/162).  
  
Update: Fixed in UUID develop branch. I'm re-triggering the CI here.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-08-17 19:47:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2294958335  

An automated preview of the documentation is available at [https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-08-18 15:57:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2295309651  

An automated preview of the documentation is available at [https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-08-19 22:42:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2297633053  

An automated preview of the documentation is available at [https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-08-22 03:32:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2303606720  

An automated preview of the documentation is available at [https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-08-23 00:07:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2305943962  

An automated preview of the documentation is available at [https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://274.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 10

> Username: k3DW  
> Created_at: 2024-08-23 04:24:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/274#issuecomment-2306210195  

At this point, all outstanding external issues have been fixed except for one affecting Cygwin in the Interprocess library, and one affecting clang-win in the Regex library. Given that there are still 2 months until Boost 1.87 closes for changes, I will go ahead and merge this PR, confident that the necessary changes can be made in the other libraries in time.

---
