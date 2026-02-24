# #19 added dbghelp.hpp [Closed]

> Username: klemens-morgenstern  
> Created at: 2016-02-13 20:50:57 UTC  
> Updated at: 2016-02-13 23:02:33 UTC  
> Closed at: 2016-02-13 23:02:33 UTC  
> Url: https://github.com/boostorg/winapi/pull/19  

Alright, as talked about in the Mailing list. here's the dbhhelp header, I matched it to the winapi structure.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-02-13 20:52:50 UTC  
> Url: https://github.com/boostorg/winapi/pull/19#issuecomment-183753248  

Test passed with MSVC 14 and Mingw 5.2.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2016-02-13 23:02:33 UTC  
> Url: https://github.com/boostorg/winapi/pull/19#issuecomment-183769852  

> AFAIK, dbghelp is not part of Windows API but part of a separate package (Windows debugging tools > or something). As such I don't think Boost.WinAPI is the right place for it.  
>   
> If multiple libraries are interested in it, you might want to ask to move it to Boost.Detail, for example.   
  
Ok, we'll move it into another folder then winapi. Might've been a bad name.

---
