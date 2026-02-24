# #841 - beast can run on arm architecture ? [Closed]

> Username: nguoithichkhampha  
> Created at: 2017-10-26 17:14:17 UTC  
> Updated at: 2017-11-24 03:00:46 UTC  
> Closed at: 2017-11-18 21:01:05 UTC  
> Url: https://github.com/boostorg/beast/issues/841  

Hi @vinniefalco , we are planning to build a websocket server on iOS and android base on beast. So, beast can run on arm architecture ? how is performance run on mobile ?

---

## Comment 1

> Username: djarek  
> Created at: 2017-10-26 17:19:53 UTC  
> Url: https://github.com/boostorg/beast/issues/841#issuecomment-339737272  

@nguoithichkhampha   
I used beast on a few embedded IoT platforms (e.g ARM Cortex-A53 in RPi 3 or QCA9550 MIPS based router SoC). I can confirm it works like charm :).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-26 17:27:42 UTC  
> Url: https://github.com/boostorg/beast/issues/841#issuecomment-339739421  

It **should** work, but note that I do not have resources for ensuring that it compiles on ARM toolchains (is there a way to set that up on Travis?)  
  
That said, I am more than happy to accept pull requests to fix any small issues that might come up. If you want Beast to have robust support for ARM then I would suggest you subscribe to the repository and test the develop and master branches from time to time. I submit new branches as a pull request so that gives users an opportunity to check out the branch and report on it before it gets merged.

---

## Comment 3

> Username: djarek  
> Created at: 2017-10-26 17:46:45 UTC  
> Updated at: 2017-10-26 17:47:00 UTC  
> Url: https://github.com/boostorg/beast/issues/841#issuecomment-339744811  

@vinniefalco   
At work we used openWRT's(now known as LEDE) buildroot for the toolchain, but it might be overkill for this purpose(downloads and compiles the toolchain for your own architecture). Perhaps [this](https://github.com/dockcross/dockcross) could be useful.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-10-26 18:00:08 UTC  
> Url: https://github.com/boostorg/beast/issues/841#issuecomment-339748614  

Well like I said I am more than happy to accept pull requests, but configuring and using a Docker image falls way outside my area of expertise and comfort.

---

## Comment 5

> Username: nguoithichkhampha  
> Created at: 2017-10-27 02:54:35 UTC  
> Url: https://github.com/boostorg/beast/issues/841#issuecomment-339858587  

@djarek , great news for me.  
@vinniefalco , thanks for your suggestion. I will try.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-11-18 21:01:05 UTC  
> Url: https://github.com/boostorg/beast/issues/841#issuecomment-345470867  

It doesn't look like there's anything actionable here so I'll go ahead and close the issue, but feel free to re-open it or create a new issue if the need arises, thanks!

---

## Comment 7

> Username: nguoithichkhampha  
> Created at: 2017-11-24 03:00:45 UTC  
> Url: https://github.com/boostorg/beast/issues/841#issuecomment-346731830  

just announce I have built successful beast on iOS and Android and it run well. But need more time to test the stability.
