# #208 Improves reconnection responsiveness [Merged]

> Username: mzimbres  
> Created at: 2024-08-18 09:26:57 UTC  
> Updated at: 2024-09-15 09:04:48 UTC  
> Merged at: 2024-09-15 09:04:48 UTC  
> Closed at: 2024-09-15 09:04:48 UTC  
> Url: https://github.com/boostorg/redis/pull/208  

As the user points out in  
  
- https://github.com/boostorg/redis/issues/205  
  
`wait_for_all` might be the cause of delay in the reconnection. I could not observe any improvement in my local setup but `wait_for_one_error` look the correct token and all tests pass so I am willing to change it.

---

## Comment 1

> Username: mzimbres  
> Created_at: 2024-08-18 09:37:09 UTC  
> Url: https://github.com/boostorg/redis/pull/208#issuecomment-2295195887  

@VemundH I can't reproduce you problems locally so you might want to test this before I merge it.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2024-08-18 09:42:59 UTC  
> Url: https://github.com/boostorg/redis/pull/208#issuecomment-2295197450  

@pdimov  I am trying to add @anarthal as a reviewer here but couldn't although I had admin rights. In Settings I clicked on `write` and now I don't even see the Settings button again. Can you help me?

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-08-18 09:52:08 UTC  
> Url: https://github.com/boostorg/redis/pull/208#issuecomment-2295200411  

Made you back an admin.

---

## Comment 4

> Username: VemundH  
> Created_at: 2024-08-19 08:21:52 UTC  
> Url: https://github.com/boostorg/redis/pull/208#issuecomment-2295961439  

@mzimbres It does not look like wait_for_one_error solved my problem. Added a comment in #205   
  
I am testing by running redis in a docker container. Then stopping and starting the container.

---
