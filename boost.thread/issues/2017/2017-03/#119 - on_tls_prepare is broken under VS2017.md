# #119 - on_tls_prepare is broken under VS2017 [Closed]

> Username: pravic  
> Created at: 2017-03-21 17:30:36 UTC  
> Updated at: 2017-08-23 06:58:32 UTC  
> Closed at: 2017-04-22 22:41:31 UTC  
> Url: https://github.com/boostorg/thread/issues/119  

Fixed in bd0379af57fa294df310221492da618844182658, but introduced in 206ff0ba7c8aba742222a341f7eba6571a392097.  
  
So, Boost 1.63 and 1.62 are broken now. I wonder what was the reason to **introduce such check** and maintain it for every new compiler version?

---

## Comment 1

> Username: viboes  
> Created at: 2017-03-21 18:06:06 UTC  
> Updated at: 2017-04-22 22:40:18 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-288168058  

Why do you say that this version were broken?  
It is not clear to know what the new version would provide .  
  
BTW, you now now a fix for the new compiler version :)

---

## Comment 2

> Username: pravic  
> Created at: 2017-03-21 18:29:02 UTC  
> Updated at: 2017-03-21 18:32:15 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-288175119  

Because that check against _MSC_VER 1900 was introduced 9 months ago, so it was included in 1.62 and 1.63. Broken for VS 2017, of course.  
  
Yes, now we have a fix, but we have to wait until 1.64 (until August?) to receive VS 2017 support. Especially because such checks exist in other boost libraries too.  
  
> It is not clear to know what the new version would provide .  
  
Oh please, how many bugs were introduced by new compiler versions? Isn't easier to fix them instead of increment supported versions each release?

---

## Comment 3

> Username: MarcelRaad  
> Created at: 2017-03-21 18:33:06 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-288176336  

The bad thing that I don't understand is that some future version with (_MSC_VER > 2000) will be broken again.

---

## Comment 4

> Username: pravic  
> Created at: 2017-03-21 18:35:49 UTC  
> Updated at: 2017-03-21 18:36:08 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-288177102  

> instead of increment supported versions each release?  
  
Well, except just to increment supported compiler versions, we need do not forget about it: [#11365](https://svn.boost.org/trac/boost/ticket/11365) appeared in 1.58 version, but was fixed only in [1.61](http://www.boost.org/users/history/version_1_61_0.html).

---

## Comment 5

> Username: viboes  
> Created at: 2017-03-21 21:34:10 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-288226135  

I'm maintaining as well as I can Boost.Thread. I have no idea of Windows.  
If some one has a solution, please propose it, post a PR.   
To all of you that are interested. Please comment the PR.  
  
I've requested help on Boost.Thread maintenance for windows several times already. If some one want to maintain this part of Boost, or even more please contact me directly.

---

## Comment 6

> Username: MarcelRaad  
> Created at: 2017-03-24 21:32:32 UTC  
> Updated at: 2017-03-24 21:32:49 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-289148160  

@viboes   
  
> I'm maintaining as well as I can Boost.Thread.  
  
Thanks for that!  
  
> If some one has a solution, please propose it, post a PR.  
  
I've posted a PR to just remove the never used codepath to avoid confusion and future breakage:  
https://github.com/boostorg/thread/pull/120

---

## Comment 7

> Username: viboes  
> Created at: 2017-03-24 23:58:13 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-289170422  

Thanks, I will merge it once the release 1.64 is delivered.

---

## Comment 8

> Username: pravic  
> Created at: 2017-04-22 17:58:23 UTC  
> Updated at: 2017-04-22 17:58:41 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-296390523  

Was it merged? Looks like [no](https://github.com/boostorg/thread/blob/master/src/win32/tss_pe.cpp#L115).

---

## Comment 9

> Username: MarcelRaad  
> Created at: 2017-04-22 18:18:39 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-296391782  

@pravic Yes, into the develop branch.

---

## Comment 10

> Username: viboes  
> Created at: 2017-04-22 22:41:31 UTC  
> Url: https://github.com/boostorg/thread/issues/119#issuecomment-296406230  

https://github.com/boostorg/thread/pull/120
