# #212 Python.jam: Postpone the computation of python "major-minor" version … [Merged]

> Username: raffienficiaud  
> Created at: 2017-07-15 13:06:33 UTC  
> Updated at: 2021-10-02 22:18:30 UTC  
> Merged at: 2017-07-17 22:21:02 UTC  
> Closed at: 2017-07-17 22:21:02 UTC  
> Url: https://github.com/boostorg/build/pull/212  

Following up my post on the ML: this fixes the issue I was having for not being able to build boost.python.  
  
The PR is based on master branch. Included some tabs to space conversion.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2017-07-15 13:08:30 UTC  
> Url: https://github.com/boostorg/build/pull/212#issuecomment-315533011  

Note that I do not know bjam much, and do not know what the line ``version = $(major-minor:J=.) ;`` is doing. Might be an issue as it is not rewritten at the beginning of the function.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2017-07-17 22:30:47 UTC  
> Url: https://github.com/boostorg/build/pull/212#issuecomment-315904071  

Thanks!

---

## Comment 3

> Username: sav-ix  
> Created_at: 2017-07-20 06:19:03 UTC  
> Url: https://github.com/boostorg/build/pull/212#issuecomment-316608020  

Thank you for fix! It works fine.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2017-07-20 12:08:34 UTC  
> Url: https://github.com/boostorg/build/pull/212#issuecomment-316683683  

Would someone please merge this to master ? (Alternatively, I may do it if no-one objects. I got confirmation from a Boost.Python user that it fixes their build issue, too.)

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2017-07-20 12:15:58 UTC  
> Url: https://github.com/boostorg/build/pull/212#issuecomment-316685207  

It's been in master for three days already.

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2017-07-20 12:16:30 UTC  
> Url: https://github.com/boostorg/build/pull/212#issuecomment-316685320  

Oh, excellent. Thanks !

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2017-07-20 18:06:36 UTC  
> Url: https://github.com/boostorg/build/pull/212#issuecomment-316784815  

@sav-ix glad to hear :)  
@grafikrobot Thanks Rene!

---
