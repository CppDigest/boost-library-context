# #497 static_assert when mingw is used in a way not supported. [Merged]

> Username: jzmaddock  
> Created at: 2021-01-22 12:19:18 UTC  
> Updated at: 2024-01-24 21:34:47 UTC  
> Merged at: 2021-01-22 18:25:33 UTC  
> Closed at: 2021-01-22 18:25:33 UTC  
> Url: https://github.com/boostorg/math/pull/497  

IT has issue with thread_local and non-trivial types.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-01-22 12:33:40 UTC  
> Url: https://github.com/boostorg/math/pull/497#issuecomment-765366483  

I wrote some thought-providing detection logic [here:](https://github.com/boostorg/multiprecision/pull/291#issuecomment-765362563)  
  
Something like (maybe?) this:  
  
```  
#if defined(__GNUC__) && defined(__MINGW__)  
  
#if (__GNUC__ < 9) && (__GNUC_MINOR__ < 2) && defined(_REENTRANT)  
static_assert(false, "Error: bad behavior");  
#endif  
  
#endif  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-01-22 13:10:25 UTC  
> Url: https://github.com/boostorg/math/pull/497#issuecomment-765386215  

I don't think we can say that it's fixed post gcc-9 because there are no gcc-9 win32-thread releases yet.  Maybe there will be, maybe it's a dead project I don't know.  MSys builds all works fine, as do older releases built with the POSIX thread model.  BTW I think your _REENTRANT logic is backwards (it *is* defined for mingw builds that are OK, and not for builds with the issue).

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-01-22 13:10:46 UTC  
> Url: https://github.com/boostorg/math/pull/497#issuecomment-765386353  

Refs https://github.com/boostorg/multiprecision/pull/291

---

## Review 4 [Commented]

> Username: ckormanyos  
> Created_at: 2021-01-22 14:06:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/497#pullrequestreview-574288354  

📁 include/boost/math/tools/config.hpp

```diff
 474 |+ // Some mingw flavours have issues with thread_local and types with non-trivial destructors
 475 |+ // See https://sourceforge.net/p/mingw-w64/bugs/527/
 476 |+ //
```

> Username: ckormanyos  
> Created_at: 2021-01-22 14:06:33 UTC  
> Url: https://github.com/boostorg/math/pull/497#discussion_r562653182  

Oh... Now I see... You already have worked out the logic. I could use this in the multiprecision branch for CI...

> Username: jzmaddock  
> Created_at: 2021-01-22 15:12:42 UTC  
> Updated_at: 2021-01-22 15:12:43 UTC  
> Url: https://github.com/boostorg/math/pull/497#discussion_r562698541  

Maybe not: I notice the failing CI run *does* define _REENTRANT so there's still something different going on than on my local machine :(

> Username: ckormanyos  
> Created_at: 2021-01-22 18:15:19 UTC  
> Updated_at: 2021-01-22 18:15:20 UTC  
> Url: https://github.com/boostorg/math/pull/497#discussion_r562819225  

> ... there's still something different going on than on my local machine  
  
An interesting point. The virtual machine on CI runs on `[windows-latest]` which is an alias for Windows Server 2019. Perhaps there is a different C-Library or threading model on the MinGW that somehow got put together on the machine.  
  
It is not immediately clear to me how this might be, but the C runtime library is now shipped as an integral port of a Windows distro, so there might be slight variations in that part...?


---
