# #156 - clang-win: linking errors LNK1120, LNK1112 [Open]

> Username: hia3  
> Created at: 2017-01-26 08:40:15 UTC  
> Updated at: 2017-02-21 18:40:59 UTC  
> Url: https://github.com/boostorg/build/issues/156  

I'm trying to build attached example using BB from 1.63.  
  
It builds just fine if `toolset=msvc-14.0`, but with `toolset=clang-win` I'm getting `LNK1120: unresolved external`. -d+2 shows that `user32.lib` is missing in linker `rsp` file.  
  
Building with `address-model=32` gives another linker error.  
  
[clang-min.zip](https://github.com/boostorg/build/files/731788/clang-min.zip)  
  
Thanks.

---

## Comment 1

> Username: eldiener  
> Created at: 2017-01-26 09:00:46 UTC  
> Url: https://github.com/boostorg/build/issues/156#issuecomment-275338225  

Which version of clang are you using ?

---

## Comment 2

> Username: hia3  
> Created at: 2017-01-26 10:21:32 UTC  
> Url: https://github.com/boostorg/build/issues/156#issuecomment-275354267  

This one - http://llvm.org/pre-releases/win-snapshots/LLVM-4.0.0-r291454-win64.exe

---

## Comment 3

> Username: eldiener  
> Created at: 2017-01-26 12:58:50 UTC  
> Url: https://github.com/boostorg/build/issues/156#issuecomment-275382453  

Please give your 'using toolset...', full b2 command, and information about where ( what Boost library ) you are invoking b2 from.

---

## Comment 4

> Username: hia3  
> Created at: 2017-01-26 13:19:54 UTC  
> Url: https://github.com/boostorg/build/issues/156#issuecomment-275386300  

My "using" is in `site-config.jam.my` file. Full command in `start_me.bat`. Commands should be invoked in the same directory where `start_me.bat` lives.

---

## Comment 5

> Username: eldiener  
> Created at: 2017-01-31 21:12:00 UTC  
> Url: https://github.com/boostorg/build/issues/156#issuecomment-276493726  

The WinMain function is missing starting and ending braces. Please correct and try again.

---

## Comment 6

> Username: hia3  
> Created at: 2017-02-02 05:35:08 UTC  
> Url: https://github.com/boostorg/build/issues/156#issuecomment-276872752  

No, they are not missing. This thing called [function-try-block](http://en.cppreference.com/w/cpp/language/function-try-block).

---

## Comment 7

> Username: eldiener  
> Created at: 2017-02-20 20:19:19 UTC  
> Url: https://github.com/boostorg/build/issues/156#issuecomment-281172851  

What I am seeing is that when using clang-win, as opposed to msvc, Boost Build is trying to build user32 and gdi32 instead of treating these libs as existing and using them when building test1. I queried about this in the appropriate Boost mailing lists but never received an answer about it from people who are more knowledgeable about the internals of Boost Build.

---

## Comment 8

> Username: hia3  
> Created at: 2017-02-21 18:40:59 UTC  
> Url: https://github.com/boostorg/build/issues/156#issuecomment-281436698  

![image](https://cloud.githubusercontent.com/assets/4704208/23179245/404fc17c-f87e-11e6-943f-1bf8c5614833.png)
