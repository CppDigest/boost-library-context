# #53 - Sparring_partner.exe crashed when execute 2 tests(bind_stdin_stdout and exit_code) on windows [Closed]

> Username: PhoebeHui  
> Created at: 2018-10-11 08:18:08 UTC  
> Updated at: 2019-04-10 04:09:37 UTC  
> Closed at: 2019-04-10 04:09:37 UTC  
> Url: https://github.com/boostorg/process/issues/53  

We tried to build and run process test with visual studio latest release on Windows. Sparring_partner.exe crashed when execute 2 tests(bind_stdin_stdout and exit_code), could you please help take a look at this? Thank you!  
Reproduce steps:  
1.	git clone -c core.autocrlf=true --recursive https://github.com/boostorg/boost.git D:\Boost\src  
2.	open a VS 2017 x86 command prompt and browse to D:\Boost\src  
3.	.\bootstrap  
4.	.\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
5.	.\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6.	.\b2 -j4 variant=release –d+2 --build-dir=..\out\x86rel libs\process\test  
  
Sparring_partner.exe crashed when execute steps  "..\out\x86rel\boost\bin.v2\libs\process\test\exit_code.test\msvc-14.1\release\threading-multi\exit_code.exe"  "..\out\x86rel\boost\bin.v2\libs\process\test\msvc-14.1\release\threading-multi\sparring_partner.exe" > "..\out\x86rel\boost\bin.v2\libs\process\test\exit_code.test\msvc-14.1\release\threading-multi\exit_code.output" 2>&1”   
  
It also crashed when run ‘bind_stdin_stdout’ test.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-10-11 08:20:51 UTC  
> Url: https://github.com/boostorg/process/issues/53#issuecomment-428864725  

What happens if you launch `sparring_partner` independently?

---

## Comment 2

> Username: PhoebeHui  
> Created at: 2018-10-11 08:55:11 UTC  
> Url: https://github.com/boostorg/process/issues/53#issuecomment-428875718  

It doesn't crash, the ERRORLEVEL is 0.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-10-11 12:05:30 UTC  
> Url: https://github.com/boostorg/process/issues/53#issuecomment-428929994  

Ok, I'll see if can reproduce it.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2019-04-10 04:09:37 UTC  
> Url: https://github.com/boostorg/process/issues/53#issuecomment-481525620  

This happens because `sparring_partner` calls `std::abort` - this is not a bug, just press ignore.
