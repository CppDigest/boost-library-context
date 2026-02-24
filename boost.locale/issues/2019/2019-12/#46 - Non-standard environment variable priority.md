# #46 - Non-standard environment variable priority [Closed]

> Username: giomasce  
> Created at: 2019-12-09 14:05:13 UTC  
> Updated at: 2023-01-07 17:46:36 UTC  
> Closed at: 2023-01-07 17:46:36 UTC  
> Url: https://github.com/boostorg/locale/issues/46  

[Current Boost.Locale code](https://github.com/boostorg/locale/blob/develop/src/util/default_locale.cpp#L32) uses, in this order, environment variables `LC_CTYPE`, `LC_ALL` and `LANG` to detect the default system locale. This is different from the standard behaviour, which is to use, in the order, `LC_ALL`, `LC_*` and `LANG` (see, for instance, the [POSIX reference](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap08.html#tag_08_02)).  
  
I think it would be great for Boost to adhere to the standard behaviour.  
  
This bug was first reported to the Debian bug tracking system: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=893884

---

## Comment 1

> Username: dimztimz  
> Created at: 2020-10-04 11:26:15 UTC  
> Url: https://github.com/boostorg/locale/issues/46#issuecomment-703241236  

Please see patch #22. Note that that is minimal fix, it just switches LC_CTYPE and LC_ALL. The best fix is to create each facet with its matching environment variable, not just with LC_CTYPE.

---

## Comment 2

> Username: dimztimz  
> Created at: 2023-01-07 17:45:39 UTC  
> Url: https://github.com/boostorg/locale/issues/46#issuecomment-1374548633  

#22 was closed, this should be closed too.

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-01-07 17:46:36 UTC  
> Url: https://github.com/boostorg/locale/issues/46#issuecomment-1374548836  

Fixed by #144
