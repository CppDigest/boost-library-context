# #352 Fix posix basic_pipe move operator= _sink/_source leak [Closed]

> Username: JackDrogon  
> Created at: 2024-01-23 06:22:48 UTC  
> Updated at: 2024-03-31 22:36:34 UTC  
> Closed at: 2024-03-31 22:36:34 UTC  
> Url: https://github.com/boostorg/process/pull/352  

Fix posix basic_pipe move operator= _sink/_source leak

---

## Comment 1

> Username: JackDrogon  
> Created_at: 2024-02-04 05:47:58 UTC  
> Url: https://github.com/boostorg/process/pull/352#issuecomment-1925590474  

@klemens-morgenstern can u help me review the code?

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2024-02-05 11:32:11 UTC  
> Url: https://github.com/boostorg/process/pull/352#issuecomment-1926774142  

What's the difference?

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2024-03-31 22:36:34 UTC  
> Url: https://github.com/boostorg/process/pull/352#issuecomment-2028934951  

I don't see any semantic changes, except for throwing from a dtor.

---
