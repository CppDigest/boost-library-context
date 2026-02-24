# #378 Add tests pre-check for B2 [Merged]

> Username: Kojoley  
> Created at: 2018-01-01 21:26:15 UTC  
> Updated at: 2018-02-03 02:41:40 UTC  
> Merged at: 2018-01-01 22:37:30 UTC  
> Closed at: 2018-01-01 22:37:30 UTC  
> Url: https://github.com/boostorg/hana/pull/378  



---

## Comment 1

> Username: ldionne  
> Created_at: 2018-01-03 17:17:05 UTC  
> Url: https://github.com/boostorg/hana/pull/378#issuecomment-355069475  

This seems to have broken our Travis build: https://travis-ci.org/boostorg/hana/jobs/323956679#L4190. Any ideas?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-01-03 17:35:58 UTC  
> Url: https://github.com/boostorg/hana/pull/378#issuecomment-355074508  

Oops.  
I have checked `libs/config/checks/config.jam` of `boost_1_65_1.tar.gz` the travis is downloading and it is here.  
I am wondering if `import config : requires ;` is supported only by the latest b2, while older versions only worked with `import ../../config/checks/config : requires ;`?

---

## Comment 3

> Username: ldionne  
> Created_at: 2018-01-22 01:19:34 UTC  
> Url: https://github.com/boostorg/hana/pull/378#issuecomment-359300357  

I think this is because we don't actually build Hana as a subdirectory of the Boost tree. I'm trying to tweak the Travis [here](https://github.com/boostorg/hana/commit/b156c68e8f4d96d1e9cf4d10e27485348242f727).

---

## Comment 4

> Username: ldionne  
> Created_at: 2018-02-03 02:41:40 UTC  
> Url: https://github.com/boostorg/hana/pull/378#issuecomment-362769868  

FYI, this has been fixed by https://github.com/boostorg/hana/pull/385

---
