# #11 - Add AUTH option to connection [Closed]

> Username: Eddie-cz  
> Created at: 2022-06-27 10:11:24 UTC  
> Updated at: 2022-07-10 19:14:01 UTC  
> Closed at: 2022-07-10 19:14:00 UTC  
> Url: https://github.com/boostorg/redis/issues/11  

I miss option to assign redis password to connection to properly AUTH.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-09 08:28:52 UTC  
> Url: https://github.com/boostorg/redis/issues/11#issuecomment-1179503423  

You can add AUTH as a normal command in the request, please see https://github.com/mzimbres/aedis/blob/f8ff3034f4250b5b3fa45d713a79dd2274d61a9e/examples/containers.cpp#L33

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2022-07-09 14:44:11 UTC  
> Url: https://github.com/boostorg/redis/issues/11#issuecomment-1179556011  

It seems to negotiate resp3, you need to authenticate with call to HELLO 3...

---

## Comment 3

> Username: mzimbres  
> Created at: 2022-07-09 20:14:59 UTC  
> Url: https://github.com/boostorg/redis/issues/11#issuecomment-1179600405  

Fixed in master, see https://github.com/mzimbres/aedis/blob/6ca0bcc945e306599b9b686971c8c26897e80dda/examples/containers.cpp#L34

---

## Comment 4

> Username: mzimbres  
> Created at: 2022-07-10 19:08:21 UTC  
> Url: https://github.com/boostorg/redis/issues/11#issuecomment-1179782176  

This is fixed in v0.2.0 where sending HELLO 3 is now a user responsibility. If this solves your problem I will close this issue as fixed.

---

## Comment 5

> Username: Eddie-cz  
> Created at: 2022-07-10 19:14:00 UTC  
> Url: https://github.com/boostorg/redis/issues/11#issuecomment-1179782912  

To sum it up: to negotiate protocol, you need to auth first, otherwise it will fall to resp2 with auth after hello.
