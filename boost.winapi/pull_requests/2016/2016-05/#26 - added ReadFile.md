# #26 added ReadFile [Closed]

> Username: klemens-morgenstern  
> Created at: 2016-05-23 13:24:51 UTC  
> Updated at: 2016-05-29 20:07:47 UTC  
> Closed at: 2016-05-29 16:40:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/26  

Hi Andrey,  
  
I just added the ReadFile function, WriteFile was already in. Test passes and it's rather trivial.  
  
Thx,  
  
Klemens

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-05-24 09:20:13 UTC  
> Url: https://github.com/boostorg/winapi/pull/26#issuecomment-221213011  

Also added ERROR_BROKEN_PIPE constant to readfile. I added it there, not in pipes.hpp because it is an error code provided by ReadFile / WriteFile.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2016-05-28 15:43:48 UTC  
> Url: https://github.com/boostorg/winapi/pull/26#issuecomment-222314842  

Alright, I think that's all for this part. I have the environment in seperate header, which are currently in my repository, So that PR should be finished.

---

## Comment 3

> Username: Lastique  
> Created_at: 2016-05-29 16:40:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/26#issuecomment-222369984  

I added ReadFile but not ERROR_BROKEN_PIPE. I intend to add error codes in a separate header, but haven't got around to do it.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2016-05-29 20:07:47 UTC  
> Url: https://github.com/boostorg/winapi/pull/26#issuecomment-222379976  

Ok, thanks.

---
