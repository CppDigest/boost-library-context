# #80 - Diagnostic_(details|info) method visibility [Closed]

> Username: Snarpix  
> Created at: 2025-04-15 10:47:23 UTC  
> Updated at: 2025-04-15 17:28:41 UTC  
> Closed at: 2025-04-15 11:09:43 UTC  
> Url: https://github.com/boostorg/leaf/issues/80  

Latest release of LEAF removed verbose_diagnostic_info and replaced it with diagnostic_details.   
New  diagnostic_(details|info) are not usable, because of protected visibility of std::ostream & operator<<. Maybe we should add `public:` after constructors perhaps?

---

## Comment 1

> Username: Snarpix  
> Created at: 2025-04-15 11:09:43 UTC  
> Url: https://github.com/boostorg/leaf/issues/80#issuecomment-2804687110  

I'm sorry that was entirely not an issue

---

## Comment 2

> Username: zajo  
> Created at: 2025-04-15 16:33:59 UTC  
> Url: https://github.com/boostorg/leaf/issues/80#issuecomment-2806782294  

Are you saying that visibility is broken but didn't create an issue in the end, or are you saying that you thought it is broken but it isn't? I want to make sure everything is correct.

---

## Comment 3

> Username: Snarpix  
> Created at: 2025-04-15 17:28:40 UTC  
> Url: https://github.com/boostorg/leaf/issues/80#issuecomment-2806962849  

I thought that visibility was broken, but that was just missing include. My bad.  
After migrating from make_shared_context  to try_capture_all everything works.  
I'm sorry for bothering you.
