# #60 Fix codecvt and improve conversion tests [Merged]

> Username: Flamefire  
> Created at: 2020-01-08 16:25:15 UTC  
> Updated at: 2020-01-09 12:10:04 UTC  
> Merged at: 2020-01-09 12:10:00 UTC  
> Closed at: 2020-01-09 12:10:00 UTC  
> Url: https://github.com/boostorg/nowide/pull/60  

Converting an UTF-16 encoded string to UTF-8 with utf8_codecvt leads wrongly to a `ok` return value, but `partial` would be correct.

---
