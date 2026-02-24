# #274 Print config info via b2 [Merged]

> Username: Kojoley  
> Created at: 2019-04-03 00:51:56 UTC  
> Updated at: 2019-06-28 17:29:00 UTC  
> Merged at: 2019-06-26 07:36:29 UTC  
> Closed at: 2019-06-26 07:36:29 UTC  
> Url: https://github.com/boostorg/config/pull/274  

Simplifies CI build scripts. Allows having CI builds with multiple options  
like `address-model=32,64`.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-06-26 07:36:23 UTC  
> Url: https://github.com/boostorg/config/pull/274#issuecomment-505757881  

Thanks for that!

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-06-26 17:13:04 UTC  
> Url: https://github.com/boostorg/config/pull/274#issuecomment-505965103  

For some reason this caused a ton of failures when merged.  
It's a good idea but reverted for now.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-06-27 13:05:19 UTC  
> Url: https://github.com/boostorg/config/pull/274#issuecomment-506337373  

The build before reverting the PR have passed. I have no idea what was the problem, it is also passing in my fork.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-06-28 17:29:00 UTC  
> Url: https://github.com/boostorg/config/pull/274#issuecomment-506815407  

Now that everything else has stabilized, I've reapplied the patch in: https://github.com/boostorg/config/pull/290 and we'll see what happens this time.  It worked for me locally as well BTW.

---
