# #134 Fixed wrong case in #include leading to compilation error with MinGW and Posix threads [Closed]

> Username: kivadiu  
> Created at: 2017-07-12 10:48:37 UTC  
> Updated at: 2017-07-12 11:56:37 UTC  
> Closed at: 2017-07-12 11:26:27 UTC  
> Url: https://github.com/boostorg/fiber/pull/134  



---

## Comment 1

> Username: olk  
> Created_at: 2017-07-12 11:03:45 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314735700  

could you re-apply your pull-request to branch develop, please?

---

## Comment 2

> Username: pgroke-dt  
> Created_at: 2017-07-12 11:12:35 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314738427  

Are you sure that the lower "w" is more common if SDKs for cross-compiling? Because most SDKs from MS use a capital "W" nowadays...

---

## Comment 3

> Username: kivadiu  
> Created_at: 2017-07-12 11:17:47 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314739443  

> Are you sure that the lower "w" is more common if SDKs for cross-compiling? Because most SDKs from MS use a capital "W" nowadays...  
  
I use W64 5.0.2.  
What else do you know I could compare with?  
Would you therefore suggest to put it between #ifdef mingw-64 #endif?  
  
Frédéric

---

## Comment 4

> Username: kivadiu  
> Created_at: 2017-07-12 11:18:37 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314739597  

> could you re-apply your pull-request to branch develop, please?  
  
done: https://github.com/boostorg/fiber/pull/135  
  
Frédéric

---

## Comment 5

> Username: kivadiu  
> Created_at: 2017-07-12 11:22:49 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314740398  

Apparently MinGW (not W64) has also windows.h lower case in version 5.0.

---

## Comment 6

> Username: olk  
> Created_at: 2017-07-12 11:25:28 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314740913  

I'd like to get the pull-request for fiber's branch develop - without other  
commits (your pull-requests contains unrelated, previously comits).  
  
2017-07-12 13:18 GMT+02:00 Frédéric Bron <notifications@github.com>:  
  
> > could you re-apply your pull-request to branch develop, please?  
>  
> done: https://github.com/boostorg/fiber/pull/135  
>  
> Frédéric  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/pull/134#issuecomment-314739597>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AA30QKgRYqwhSkCoLzN83A-RQlADVfcWks5sNKuOgaJpZM4OVc5W>  
> .  
>

---

## Comment 7

> Username: olk  
> Created_at: 2017-07-12 11:26:27 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314741096  

wrong target branch - please provide it for branch develop

---

## Comment 8

> Username: pgroke-dt  
> Created_at: 2017-07-12 11:34:28 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314742654  

Frédéric, I don't know. For builds on Windows machines it won't be a problem. For cross-compiling ... don't know what the "common" casing is.

---

## Comment 9

> Username: kivadiu  
> Created_at: 2017-07-12 11:56:37 UTC  
> Url: https://github.com/boostorg/fiber/pull/134#issuecomment-314747114  

> wrong target branch - please provide it for branch develop  
  
New try: https://github.com/boostorg/fiber/pull/136

---
