# #2 avoid unused variable warning on non-windows builds [Closed]

> Username: jhunold  
> Created at: 2014-01-06 11:40:36 UTC  
> Updated at: 2014-06-13 07:26:42 UTC  
> Closed at: 2014-05-01 21:42:52 UTC  
> Url: https://github.com/boostorg/filesystem/pull/2  

Found by clang, colon is only checked with BOOST_WINDOWS_API

---

## Comment 1

> Username: Beman  
> Created_at: 2014-05-01 21:42:52 UTC  
> Url: https://github.com/boostorg/filesystem/pull/2#issuecomment-41960432  

Closed without pulling because a simpler fix was just to delete the line of code - it was already in an #ifdef.  
  
Thanks,  
  
--Beman

---
