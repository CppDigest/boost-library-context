# #811 - Is the minimum required version of cmake really important is 3.5.2? [Closed]

> Username: d3roch4  
> Created at: 2017-10-09 19:34:52 UTC  
> Updated at: 2017-10-20 17:08:13 UTC  
> Closed at: 2017-10-20 17:08:13 UTC  
> Url: https://github.com/boostorg/beast/issues/811  

In ubuntu 16.04, the default version of cmake is 3.5.1 and the new version of compile the new version of much work.  
I've created a fork just to change the `cmake_minimum_required (VERSION 3.5.1)` line and it's working for me.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-09 20:04:23 UTC  
> Url: https://github.com/boostorg/beast/issues/811#issuecomment-335270869  

CMake works for you on Ubuntu? Which version of Beast are you using?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-09 21:34:56 UTC  
> Url: https://github.com/boostorg/beast/issues/811#issuecomment-335297127  

I can bump it down and see what happens

---

## Comment 3

> Username: d3roch4  
> Created at: 2017-10-14 01:09:54 UTC  
> Url: https://github.com/boostorg/beast/issues/811#issuecomment-336599045  

Isso use beast Master git in Ubuntu 16.04 with cmake 3.5.1  
  
  
Em 09/10/2017 17:04, "Vinnie Falco" <notifications@github.com> escreveu:  
  
CMake works for you on Ubuntu? Which version of Beast are you using?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub  
<https://github.com/boostorg/beast/issues/811#issuecomment-335270869>, or mute  
the thread  
<https://github.com/notifications/unsubscribe-auth/AFqHDZn-6CIiyJTsHtGNxKjzULFH5qaxks5sqnxKgaJpZM4Py9NF>  
.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-10-14 04:20:58 UTC  
> Url: https://github.com/boostorg/beast/issues/811#issuecomment-336608747  

Okay, well I pushed a new version that lowers the requirements to 3.5.1 and it seems okay. Does it work for you?

---

## Comment 5

> Username: d3roch4  
> Created at: 2017-10-17 18:32:32 UTC  
> Url: https://github.com/boostorg/beast/issues/811#issuecomment-337325866  

Yes!  
Thank You  
  
2017-10-14 1:21 GMT-03:00 Vinnie Falco <notifications@github.com>:  
  
> Okay, well I pushed a new version that lowers the requirements to 3.5.1  
> and it seems okay. Does it work for you?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/811#issuecomment-336608747>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AFqHDX_dDSipxoVgcij6IE9DNCM3jgnKks5ssDatgaJpZM4Py9NF>  
> .  
>

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-10-17 18:33:45 UTC  
> Url: https://github.com/boostorg/beast/issues/811#issuecomment-337326253  

Glad to hear it!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-10-20 17:08:13 UTC  
> Url: https://github.com/boostorg/beast/issues/811#issuecomment-338266178  

Looks like we're set here so I'll go ahead and close the issue, thanks!
