# #77 Silence MSVC warnings about conditional expression being constant. [Merged]

> Username: Lastique  
> Created at: 2018-02-04 21:18:47 UTC  
> Updated at: 2018-02-13 16:19:27 UTC  
> Merged at: 2018-02-11 04:42:45 UTC  
> Closed at: 2018-02-11 04:42:45 UTC  
> Url: https://github.com/boostorg/date_time/pull/77  

The warning is emitted whereever `std::numeric_limits<>::is_signed` is used in a conditional statement.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-02-04 21:22:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/77#issuecomment-362940819  

This seems like the compiler is being dumb.  Looks fine to me.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-02-11 04:42:40 UTC  
> Url: https://github.com/boostorg/date_time/pull/77#issuecomment-364721074  

I believe it only happens with /W4, but yes, it's intentional and can be disabled.

---
