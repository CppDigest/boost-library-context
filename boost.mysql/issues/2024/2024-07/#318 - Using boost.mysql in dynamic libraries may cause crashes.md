# #318 - Using boost.mysql in dynamic libraries may cause crashes [Closed]

> Username: jphz  
> Created at: 2024-07-16 14:59:28 UTC  
> Updated at: 2024-07-22 13:35:38 UTC  
> Closed at: 2024-07-22 13:35:38 UTC  
> Url: https://github.com/boostorg/mysql/issues/318  

![image](https://github.com/user-attachments/assets/539169be-cbcd-4893-b4ad-fe3cfb33a302)  
Triggered an unprocessed exception: write access permission conflict.  
N is null ptr.

---

## Comment 1

> Username: jphz  
> Created at: 2024-07-16 14:59:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/318#issuecomment-2231137959  

There will not be such issues when using in a static library.

---

## Comment 2

> Username: jphz  
> Created at: 2024-07-16 15:00:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/318#issuecomment-2231138723  

I am very confused and hope to receive an answer.

---

## Comment 3

> Username: jphz  
> Created at: 2024-07-16 15:01:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/318#issuecomment-2231141546  

![image](https://github.com/user-attachments/assets/0e759224-2700-4f5b-b56a-efb3c19a94a9)  
thread status

---

## Comment 4

> Username: anarthal  
> Created at: 2024-07-16 16:48:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/318#issuecomment-2231385928  

A possible cause for your problem is having different versions of your libraries in the DLL you're building and in your main. Check that:  
- You're using the same standard library for both your main and your DLL. MSVC comes with [different standard library versions](https://learn.microsoft.com/en-us/cpp/build/reference/md-mt-ld-use-run-time-library?view=msvc-170) that are not compatible with each other.  
- You're using different configuration switches for your main and your DLL. For instance, defining a BOOST_ASIO_XXXX macro on one, and not the other.  
  
If that doesn't solve the problem, I'd need more information to investigate. That'd include your compiler version, build flags, whether you're using Boost.MySQL only in your DLL or also in your main, and whether you're using other Boost libraries in your main.

---

## Comment 5

> Username: anarthal  
> Created at: 2024-07-22 10:18:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/318#issuecomment-2242605941  

Any news on this? Was the issue solved?

---

## Comment 6

> Username: jphz  
> Created at: 2024-07-22 10:22:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/318#issuecomment-2242612697  

There was a problem with my code writing, but it has been resolved. I am very sorry.

---

## Comment 7

> Username: anarthal  
> Created at: 2024-07-22 13:35:38 UTC  
> Url: https://github.com/boostorg/mysql/issues/318#issuecomment-2242980659  

Closing this now.
