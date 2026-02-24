# #106 - Problem with async_read [Closed]

> Username: cyboy22  
> Created at: 2023-05-29 13:19:10 UTC  
> Updated at: 2023-05-29 20:55:19 UTC  
> Closed at: 2023-05-29 20:55:19 UTC  
> Url: https://github.com/boostorg/redis/issues/106  

Hi,  
I am trying to build a simple program (on Mac OS X - 1 source file only - see attached file) to test the library.  
However, I am getting an error regarding the call to async_exec.  
Would you be able to suggest where I am going wrong or let me know if you need any further information?  
![Screenshot 2023-05-29 at 13 35 11](https://github.com/boostorg/redis/assets/4527533/e258bde7-49c3-462d-a0ea-172dc40b3e23)

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-05-29 15:34:54 UTC  
> Url: https://github.com/boostorg/redis/issues/106#issuecomment-1567282535  

Can you replace  
  
```cpp  
conn->async_exec(req, resp);  
```  
with  
```cpp  
conn->async_exec(req, resp, deferred);  
```  
  
Let me know if that works for you.

---

## Comment 2

> Username: cyboy22  
> Created at: 2023-05-29 17:17:42 UTC  
> Url: https://github.com/boostorg/redis/issues/106#issuecomment-1567363832  

Hi,  
  
Thanks - the immediate error goes away to be replaced by a number of linker errors. I suspect I am neglecting to include some other headers but am not sure which. Could you help?  
![Screenshot 2023-05-29 at 18 15 30](https://github.com/boostorg/redis/assets/4527533/5bdd369c-1003-40a5-99db-93caedf66a8b)

---

## Comment 3

> Username: cyboy22  
> Created at: 2023-05-29 19:16:59 UTC  
> Url: https://github.com/boostorg/redis/issues/106#issuecomment-1567443905  

Apologies - I think you meant   "co_await conn->async_exec(req, resp, deferred);" - however I still get the linking errors.

---

## Comment 4

> Username: mzimbres  
> Created at: 2023-05-29 19:18:35 UTC  
> Url: https://github.com/boostorg/redis/issues/106#issuecomment-1567444791  

Inclue de following in one of your cpp files:` #include <boost/redis/src.hpp>`.

---

## Comment 5

> Username: cyboy22  
> Created at: 2023-05-29 19:48:10 UTC  
> Url: https://github.com/boostorg/redis/issues/106#issuecomment-1567462557  

Brilliant - it worked.
