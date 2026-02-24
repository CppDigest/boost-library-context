# #351 - Build 32-bit for gcc 4.8 [Closed]

> Username: vinniefalco  
> Created at: 2017-05-10 16:50:06 UTC  
> Updated at: 2018-04-27 00:49:46 UTC  
> Closed at: 2018-04-27 00:49:46 UTC  
> Url: https://github.com/boostorg/beast/issues/351  

It would be nice for the gcc 4.8 Travis target to build for the 32-bit address model

---

## Comment 1

> Username: brunocodutra  
> Created at: 2017-05-10 20:17:33 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-300600123  

I've never set up travis to cross-compile to 32-bit, so I'm afraid I can't help you much, but what happens if you install gcc-multilib in addition to gcc?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-10 20:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-300600821  

It screws up. I'm not exactly sure how to specify it, how does this look?  
```  
    # older GCC, 32-bit release  
    - compiler: gcc  
      env:  
        - GCC_VER=4.8  
        - VARIANT=release  
        - ADDRESS_MODEL=32  
      addons:  
        apt:  
          packages:  
            - gcc-4.8  
            - gcc-multilib  
            - g++-4.8  
            - *base_packages  
          sources:  
            - *base_sources  
```

---

## Comment 3

> Username: brunocodutra  
> Created at: 2017-05-10 20:21:58 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-300601240  

try adding `gcc-4.8-multilib` and `g++-4.8-multilib`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-05-10 20:25:10 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-300602028  

Okay. Here goes:  
https://travis-ci.org/vinniefalco/Beast/builds/230915641  
  
Branch here:  
https://github.com/vinniefalco/Beast/commits/32bit

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-05-10 20:31:07 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-300603539  

BOOM  
https://travis-ci.org/vinniefalco/Beast/jobs/230915643

---

## Comment 6

> Username: brunocodutra  
> Created at: 2017-05-10 20:31:53 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-300603738  

At least the error changed, there must be some further packages missing, maybe http://stackoverflow.com/questions/14795608/asm-errno-h-no-such-file-or-directory

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-05-10 20:32:54 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-300603980  

Why is it so difficult?

---

## Comment 8

> Username: brunocodutra  
> Created at: 2017-05-11 20:40:43 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-300910748  

Frankly I don't think it's nearly as hard as setting up Appveyor ;)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-07-04 02:04:39 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-312762992  

Still a problem unfortunately

---

## Comment 10

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:33 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-384022611  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-04-27 00:49:46 UTC  
> Url: https://github.com/boostorg/beast/issues/351#issuecomment-384830862  

I'm going to go ahead and give up on this since it seems to be a time-sink with very little to offer in return.
