# #20 - Supported platforms + compilers missing in documentation [Closed]

> Username: schorsch1976  
> Created at: 2017-10-24 05:47:50 UTC  
> Updated at: 2017-10-24 06:34:21 UTC  
> Closed at: 2017-10-24 06:34:21 UTC  
> Url: https://github.com/boostorg/process/issues/20  

As i can not find the supported platforms and compilers in the documentation, i am not sure if it is the reason why i cant compile the intro.cpp from the examples folder or if it is a bug in boost::process.  
  
My platform:  
  
- Win7 64 Bit  
- Visual Studio 2013  
  
I get the following error:  
  
```  
1>  intro.cpp  
1>d:\entwicklung\3rdparty\boost\1.65.1\boost\process\detail\config.hpp(65): error C3646: "noexcept": Unbekannter Überschreibungsspezifizierer  
1>d:\entwicklung\3rdparty\boost\1.65.1\boost\process\detail\config.hpp(72): error C2660: 'boost::process::process_error::process_error': Funktion akzeptiert keine 2 Argumente  
1>d:\entwicklung\3rdparty\boost\1.65.1\boost\process\detail\config.hpp(77): error C2440: '<function-style-cast>': 'std::error_code' kann nicht in 'boost::process::process_error' konvertiert werden  
```  
  
I am on   
$ git log -n1  
commit 4075371cbe78dab8f43578c655747c831b554318 (HEAD, tag: boost-1.65.1, tag: boost-1.65.0, tag: boost-1.64.0)  
Merge: 477a60e d5d2c7b  
Author: Klemens Morgenstern <klemens-morgenstern@users.noreply.github.com>  
Date:   Wed Apr 5 11:32:27 2017 +0200  
  
    Merge pull request #3 from boostorg/develop  
  
    extension adding fix for windows

---

## Comment 1

> Username: schorsch1976  
> Created at: 2017-10-24 05:55:21 UTC  
> Url: https://github.com/boostorg/process/issues/20#issuecomment-338882079  

I get the same error with the downloaded boost 1.65.1.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2017-10-24 06:34:21 UTC  
> Url: https://github.com/boostorg/process/issues/20#issuecomment-338888439  

It says in the readme that it is a C++11 library and your compiler does not support all the necessary language features. Otherwise it should support windows (up from XP) and posix as platforms.
