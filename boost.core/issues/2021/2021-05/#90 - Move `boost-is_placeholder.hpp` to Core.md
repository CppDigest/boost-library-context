# #90 - Move `boost/is_placeholder.hpp` to Core? [Closed]

> Username: Kojoley  
> Created at: 2021-05-30 02:09:52 UTC  
> Updated at: 2026-01-02 11:41:10 UTC  
> Closed at: 2026-01-02 11:41:10 UTC  
> Url: https://github.com/boostorg/core/issues/90  

Beast, Lambda, Phoenix would not depend on Bind after such a change.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-05-30 02:18:29 UTC  
> Url: https://github.com/boostorg/core/issues/90#issuecomment-850928690  

Bind only depends on Config and Core so not much of a win. Lambda currently has 26 dependencies, Phoenix 32, Beast 55.  
  
https://pdimov.github.io/boostdep-report/develop/module-weights.html

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-05-30 02:23:12 UTC  
> Url: https://github.com/boostorg/core/issues/90#issuecomment-850929063  

That's still about a megabyte repository for three lines of code. I believe it is not a large burden to transfer that header, is it?

---

## Comment 3

> Username: pdimov  
> Created at: 2025-12-14 20:16:10 UTC  
> Url: https://github.com/boostorg/core/issues/90#issuecomment-3651991227  

Any objections to moving `boost::is_placeholder` to Core for the reasons stated?

---

## Comment 4

> Username: Lastique  
> Created at: 2025-12-14 21:32:47 UTC  
> Url: https://github.com/boostorg/core/issues/90#issuecomment-3652208681  

Fine by me. But there needs to be a documentation for it, specifically on the protocol it implements.
