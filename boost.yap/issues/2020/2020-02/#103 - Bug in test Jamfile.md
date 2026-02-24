# #103 - Bug in test Jamfile [Closed]

> Username: Flamefire  
> Created at: 2020-02-20 19:35:11 UTC  
> Updated at: 2020-04-09 15:29:19 UTC  
> Closed at: 2020-04-09 08:53:52 UTC  
> Url: https://github.com/boostorg/yap/issues/103  

It seems https://github.com/boostorg/yap/blob/3771507b1d88f883092cbfcff327c688d9488244/test/Jamfile.v2#L8 is wrong.  
I tried to copy that for my project but it failed as it can't find `requires in config`.  
  
Apparently `import ../../config/checks/config : requires ;` is the correct one

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-04-08 23:31:23 UTC  
> Url: https://github.com/boostorg/yap/issues/103#issuecomment-611246188  

This has been cycling for some time in the Boost test runners with no problem.  I'm frankly afraid to touch it.  Do you know of a concrete problem that this fixes?

---

## Comment 2

> Username: Flamefire  
> Created at: 2020-04-09 08:53:52 UTC  
> Url: https://github.com/boostorg/yap/issues/103#issuecomment-611414091  

Very strange. I tried it and it works for yap. But when I tried it in nowide it did not. It does now so I guess I did something wrong or something has changed. Sorry for the noise, closed.  
  
Main confusion from my side came from the missing CI tests inside this library with b2. If you want: Use boost-ci with AzP. It doesn't interfere with your travis/appveyor tests so you can simply periodically update the yaml file and let boost-ci do the work for you.

---

## Comment 3

> Username: tzlaine  
> Created at: 2020-04-09 15:29:19 UTC  
> Url: https://github.com/boostorg/yap/issues/103#issuecomment-611591695  

No worries, Jam is hard. :)
