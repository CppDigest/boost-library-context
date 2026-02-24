# #26 Replace unsafe library function localtime by safe function localtime_… [Merged]

> Username: DanielaE  
> Created at: 2017-05-04 08:46:28 UTC  
> Updated at: 2018-01-02 10:22:55 UTC  
> Merged at: 2017-05-04 23:24:35 UTC  
> Closed at: 2017-05-04 23:24:35 UTC  
> Url: https://github.com/boostorg/chrono/pull/26  

…s when compiled with msvc 8.0 or later.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-05-04 08:48:25 UTC  
> Url: https://github.com/boostorg/chrono/pull/26#issuecomment-299128974  

In case of nobody caring about msvc 7.1 or older, the #if-branch for these compilers may be dropped altogether.

---

## Comment 2

> Username: viboes  
> Created_at: 2017-05-04 17:29:48 UTC  
> Url: https://github.com/boostorg/chrono/pull/26#issuecomment-299254238  

Thanks for the fix.

---
