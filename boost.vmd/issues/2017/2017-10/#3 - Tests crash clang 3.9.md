# #3 - Tests crash clang 3.9 [Closed]

> Username: pdimov  
> Created at: 2017-10-12 23:57:27 UTC  
> Updated at: 2018-10-29 03:37:01 UTC  
> Closed at: 2018-10-29 03:37:01 UTC  
> Url: https://github.com/boostorg/vmd/issues/3  

As seen on TV here: https://travis-ci.org/boostorg/boost/jobs/286306832

---

## Comment 1

> Username: eldiener  
> Created at: 2017-10-13 01:28:03 UTC  
> Url: https://github.com/boostorg/vmd/issues/3#issuecomment-336325252  

I am aware of this. I reported a bug to clang a long time ago that it could not handle certain VMD preprocessing, whereas gcc handles it fine. The only change that was done in clang was better error messages, but evidently no change was made to fix the problem. I reported the error on the Windows implementation of clang targeting gcc. I can see that the same problem occurs in a number of places when testing clang under Linux. I admit I am loath to report this error again, since they have not tried to fix this in the past. Perhaps the failing test is not against the latest version of clang under Linux supported by Travis CI but I am unsure how or if I can find out what the latest version of clang supported by Travis CI actually is.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-13 01:33:49 UTC  
> Url: https://github.com/boostorg/vmd/issues/3#issuecomment-336325964  

Travis has had 4.0 for a while and it apparently has 5.0 now. On the superproject I only use the version installed by default though, which is either 3.5 or 3.9 depending on the phase of the moon.  
  
Maybe you should disable these tests for clang-3 then? A requirement of `<toolset>clang-3:<build>no` in the Jamfile will do it.

---

## Comment 3

> Username: eldiener  
> Created at: 2017-10-13 01:40:42 UTC  
> Url: https://github.com/boostorg/vmd/issues/3#issuecomment-336326829  

I will do that. But I strongly suspect that clang-4.0 and clang-5.0 under Linux still has the same problem, since the same versions fail under Windows.

---

## Comment 4

> Username: pdimov  
> Created at: 2017-10-13 01:44:53 UTC  
> Url: https://github.com/boostorg/vmd/issues/3#issuecomment-336327306  

`<toolset>clang:<build>no` is always an option. :-)

---

## Comment 5

> Username: eldiener  
> Created at: 2018-10-29 03:37:01 UTC  
> Url: https://github.com/boostorg/vmd/issues/3#issuecomment-433779093  

I did do as you suggested a while back.
