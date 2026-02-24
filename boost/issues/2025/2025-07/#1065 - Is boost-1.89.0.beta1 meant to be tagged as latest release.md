# #1065 - Is boost-1.89.0.beta1 meant to be tagged as latest release? [Closed]

> Username: userdocs  
> Created at: 2025-07-17 20:08:02 UTC  
> Updated at: 2025-08-05 22:21:21 UTC  
> Closed at: 2025-08-05 22:21:21 UTC  
> Url: https://github.com/boostorg/boost/issues/1065  

https://github.com/boostorg/boost/releases/latest  
  
https://github.com/boostorg/boost/releases/tag/boost-1.89.0.beta1  
  
I though only stable was latest release?

---

## Comment 1

> Username: mclow  
> Created at: 2025-07-17 21:47:40 UTC  
> Url: https://github.com/boostorg/boost/issues/1065#issuecomment-3085626935  

If you look at the releases in https://github.com/boostorg/boost/releases/, you''ll see all the beta releases (as well as the final ones)

---

## Comment 2

> Username: userdocs  
> Created at: 2025-07-17 21:52:32 UTC  
> Updated at: 2025-08-02 08:50:44 UTC  
> Url: https://github.com/boostorg/boost/issues/1065#issuecomment-3085637543  

Yes, I appreciate that, What I'm saying is that since was the beta meant to be tagged the "latest" release which is usually reserved for stable tags, like `boost-1.88.0`  
  
https://github.com/boostorg/boost/tags  
  
There was a choice when the release was created to make it latest or just a release.  
  
https://github.com/boostorg/boost/releases/latest points to `boost-1.89.0.beta1` when i'd expect it to point to `boost-1.88.0` until  `boost-1.89.0` stable is released

---

## Comment 3

> Username: userdocs  
> Created at: 2025-08-01 23:01:32 UTC  
> Updated at: 2025-08-01 23:02:20 UTC  
> Url: https://github.com/boostorg/boost/issues/1065#issuecomment-3145957777  

This is your latest release? https://www.boost.org/releases/latest/  
  
Clearly this says 1.88.0 is the latest release.  
  
https://archives.boost.io/release/ there is no 1.89.0-beta in that folder.  
  
Github says 1.89.0-beta is latest release.  
  
By putting beta as latest release, which is optional, you have broken the latest release url redirection provided by Github.  
  
Please don't make beta latest releases. Make them pre releases.

---

## Comment 4

> Username: userdocs  
> Created at: 2025-08-01 23:42:38 UTC  
> Url: https://github.com/boostorg/boost/issues/1065#issuecomment-3146014998  

I proposed a fix here  
  
https://github.com/boostorg/boost/pull/1068
