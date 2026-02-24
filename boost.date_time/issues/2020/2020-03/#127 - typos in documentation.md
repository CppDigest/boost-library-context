# #127 - typos in documentation [Closed]

> Username: Xeverous  
> Created at: 2020-03-04 10:01:08 UTC  
> Updated at: 2020-03-08 00:29:23 UTC  
> Closed at: 2020-03-08 00:29:22 UTC  
> Url: https://github.com/boostorg/date_time/issues/127  

https://www.boost.org/doc/libs/1_72_0/doc/html/date_time/posix_time.html#date_time.posix_time.ptime_class  
  
- `tm_isdst` vs `tm_isddst` (just ctrl+F them)  
- `ptime microsec_clock::local_time()` and `ptime microsec_clock::universal_time()` have: *On Unix systems this is implemented using GetTimeOfDay. On most Win32 platforms it is implemented using ftime.*

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-03-04 14:19:16 UTC  
> Url: https://github.com/boostorg/date_time/issues/127#issuecomment-594550120  

Thanks.  tm_isddst will be fixed on branch for issue #123. Not sure I'm following the suggestion on the second bullet?

---

## Comment 2

> Username: Xeverous  
> Created at: 2020-03-04 20:19:50 UTC  
> Url: https://github.com/boostorg/date_time/issues/127#issuecomment-594813282  

The documentation mentioned on the second bullet has wrong (reversed) function names.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-03-04 23:54:05 UTC  
> Url: https://github.com/boostorg/date_time/issues/127#issuecomment-594946003  

So you're saying ftime on *nix?  Look at date_time/microsecond_clock.hpp -- ftime is winapi and GetTimeOfDay is *nix. Technically for Windows it's:  
   
      boost::winapi::FILETIME_ ft;  
      boost::winapi::GetSystemTimeAsFileTime(&ft);  
  
So I think the documentation is accurate.

---

## Comment 4

> Username: Xeverous  
> Created at: 2020-03-05 09:02:45 UTC  
> Url: https://github.com/boostorg/date_time/issues/127#issuecomment-595109748  

Ok, I haven't checked the code before the issue but seeing:  
  
- "on most Win32 platforms / on Unix systems" sounds weird/reversed as Unix has much more OS variants than Windows  
- Functions on Unix systems usually come from POSIX, so a name like `ftime` seemed analogical to eg `fopen`, `fwrite`. WinAPI has PascalCase names so that was another suspicion that names are reversed in documentation.

---

## Comment 5

> Username: JeffGarland  
> Created at: 2020-03-05 15:58:46 UTC  
> Url: https://github.com/boostorg/date_time/issues/127#issuecomment-595304879  

yes, the camel case is misleading:  
  
http://man7.org/linux/man-pages/man2/gettimeofday.2.html

---

## Comment 6

> Username: Xeverous  
> Created at: 2020-03-05 20:14:52 UTC  
> Url: https://github.com/boostorg/date_time/issues/127#issuecomment-595425768  

Ahh, now it looks like a unix function. I think you should correct it too, C++ is case-sensitive.

---

## Comment 7

> Username: JeffGarland  
> Created at: 2020-03-08 00:29:22 UTC  
> Url: https://github.com/boostorg/date_time/issues/127#issuecomment-596152092  

Fixed in commit 415dd10.  Will be part of boost 1.73.
