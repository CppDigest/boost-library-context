# #487 - Ubuntu 18.04 to be deprecated in GHA [Closed]

> Username: ckormanyos  
> Created at: 2022-08-22 14:43:59 UTC  
> Updated at: 2023-01-03 10:13:44 UTC  
> Closed at: 2023-01-03 10:13:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487  

We need to be sure to look into [this line](https://github.com/boostorg/multiprecision/blob/513fd50b3b00ae89df47dd0e47014bfe4b6f7029/.github/workflows/multiprecision.yml#L391) in relation to the December planned [deprecation of Ubuntu 18.04 on GHA](https://github.com/actions/runner-images/issues/6002).  
  
I just hit one of the four hour scheduled brown-outs inadvertently and had previously been unaware of this scheduled deprecation.  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-08-23 07:52:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487#issuecomment-1223692245  

In this issue, maybe it makes sense to create a Docker impage of the older ubuntu distro with the older GCC/clang compilers pre-installed?  
  
Maybe this (or something like it) is needed in general for Boost and we could make a generic image used by multiple Boost CIs running on GHA?  
  
Unfortunately, I do not know how to do this myself.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-08-23 11:38:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487#issuecomment-1223944770  

I'm not sure what to make of this - ordinarily I would say lets deprecate anything not supported by Github, but that removes testing for all gcc versions prior to 9 :(  
  
Maybe I'll see if earlier gcc versions can be run successfully on unbuntu-20.  
  
Or we could move all those tests on to the drone runner.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2022-08-23 13:01:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487#issuecomment-1224042745  

> see if earlier gcc versions can be run successfully on unbuntu-20.  
  
Indeed. I am doing this in another project. So far on Ubuntu 20.04, I've found:  
- g++-8 yes  
- g++-9 yes  
- clang++-9 yes  
  
I'm checking for clang++8.  
  
All of these need to be installed, as we do on the fly with `sudo apt install`.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2022-08-23 13:42:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487#issuecomment-1224096374  

Revision: On Ubuntu 20.04 LTS in GHA, I can find and use:  
- g++-7  
- g++-8  
- g++-9  
- clang++-8  
- clang++-9  
  
I've installed with the following command:  
  
```sh  
sudo apt install g++-7 g++-8 g++-9 clang-8 clang-9  
```

---

## Comment 5

> Username: mborland  
> Created at: 2022-08-23 13:55:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487#issuecomment-1224113533  

I was thinking about this last night because I hit a GCC5 specific error on a math PR. At what point do we stop testing on older compilers? We continue to modernize and add newer tool chains but never discontinue old ones. I would argue whatever the vanilla CI system can provide (seems like GCC7) is sufficient.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-08-23 14:46:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487#issuecomment-1224179743  

> At what point do we stop testing on older compilers? We continue to modernize and add newer tool chains but never discontinue old ones. I would argue whatever the vanilla CI system can provide (seems like GCC7) is sufficient.  
  
As long as we don't reach that point out of frustration, I'm OK with our decisions. But I must admit, I'm rapidly reaching that point as well.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-08-23 16:22:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487#issuecomment-1224303846  

Ubuntu-18.04 is still part of Ubuntu's LTS distributions, still supported, and still on their downloads page.  It's gcc-5 I believe.  
  
For Boost.Config I've just moved everything that's done on 18.04 to drone: https://github.com/boostorg/config/pull/443.  
  
It's a right PITA when CI keeps moving about under you though!

---

## Comment 8

> Username: ckormanyos  
> Created at: 2023-01-03 10:13:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/487#issuecomment-1369593039  

Done. Correct @jzmaddock and @mborland ?
