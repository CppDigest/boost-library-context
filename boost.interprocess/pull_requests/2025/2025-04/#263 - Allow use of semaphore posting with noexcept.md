# #263 Allow use of semaphore posting with noexcept [Closed]

> Username: yrashk  
> Created at: 2025-04-24 16:21:45 UTC  
> Updated at: 2025-08-25 22:33:36 UTC  
> Closed at: 2025-08-25 22:06:26 UTC  
> Url: https://github.com/boostorg/interprocess/pull/263  

Addresses a concern in #262  
  
---  
  
Please note that this the first draft/prototype and I have no expertise with Windows (nor did I check this on Windows yet). Any advice appreciated.

---

## Comment 1

> Username: yrashk  
> Created_at: 2025-05-04 21:42:40 UTC  
> Url: https://github.com/boostorg/interprocess/pull/263#issuecomment-2849456463  

ping @igaztanaga – bumping this PR to see if there's interest in this change

---

## Comment 2

> Username: yrashk  
> Created_at: 2025-07-10 20:40:10 UTC  
> Url: https://github.com/boostorg/interprocess/pull/263#issuecomment-3058994968  

Hi @igaztanaga – wondering if you're interested in this direction? Thank you!

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2025-08-25 22:06:26 UTC  
> Url: https://github.com/boostorg/interprocess/pull/263#issuecomment-3221884222  

Adding std::error to a few functions is not a direction I would like to follow, it would make the library design inconsistent and half-baked.  
  
Changing the general error handling of the library (or supporting exceptions+error codes) is a big task. The C++ standard uses exceptions for error signalling and probably some users would prefer using boost::system error_codes, so I don't see a clear path forward.

---

## Comment 4

> Username: yrashk  
> Created_at: 2025-08-25 22:33:35 UTC  
> Url: https://github.com/boostorg/interprocess/pull/263#issuecomment-3221943135  

Thank you.  
  
My main concern is that exceptions in semaphore posting break use of this in signals – which should otherwise be safe, if they didn't use exceptions. This is why I proposed this additional API.  
  
I wonder if you'd be interested in exploring how #262 can be solved? I'd be happy to iterate on an actual solution.

---
