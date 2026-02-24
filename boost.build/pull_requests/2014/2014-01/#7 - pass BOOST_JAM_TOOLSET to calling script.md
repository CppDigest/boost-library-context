# #7 pass BOOST_JAM_TOOLSET to calling script [Closed]

> Username: dansmith65  
> Created at: 2014-01-27 07:55:31 UTC  
> Updated at: 2021-10-02 22:36:05 UTC  
> Closed at: 2014-02-01 10:06:23 UTC  
> Url: https://github.com/boostorg/build/pull/7  

A note in bootstrap.bat file mentioned that the toolset should really  
be retrieved from build.bat, but the developer didn't know how.  
  
The comment I'm referring to is here: https://github.com/boostorg/boost/blob/bd86bb8943e01c266e5c2882706f8301e9a752b7/bootstrap.bat#L34  
  
I'm not sure if that's still relevant or not, since I noticed the boostrap.bat file in this module of the project doesn't work the same.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-02-01 10:06:23 UTC  
> Url: https://github.com/boostorg/build/pull/7#issuecomment-33867947  

Thanks for the patch, I've merged it. I suppose we still need to update Boost's top-level bootstrap.bat so that it does not hardcode msvc as toolchain on windows?

---

## Comment 2

> Username: dansmith65  
> Created_at: 2014-02-01 20:32:00 UTC  
> Url: https://github.com/boostorg/build/pull/7#issuecomment-33882595  

I had a really hard time following the flow of the build.bat script. The other day when I posted this code, I wasn't entirely sure it was all that was necessary to actually pass the variable up to the calling script.  
  
I just had a look at the script again and think there is an issue with the `:Set_Args` section. It can call `setlocal` without calling `endlocal`, which will prevent my patch from having any effect. There are some other strange things going on in this script as well, for example:  
- `:Test_Path` could probably be simplified down to just one test: `if "%~1"==""`  
- `:Set_Error` and `:Clear_Error` seem to work, but they are a little convoluted, I would have preferred to use [exit /b #](http://ss64.com/nt/exit.html)  
  
I may be able to contribute some more code, if you'd like, but I know next to nothing about C++/Boost, so I may not be able to properly test it. I have been able to build boost, so I can do some basic testing. I also think I'll want to re-work the script a little so I can better understand the flow of it. Let me know if you are open to accepting these changes and working with me a little to test it.

---

## Comment 3

> Username: vprus  
> Created_at: 2014-02-05 07:43:08 UTC  
> Url: https://github.com/boostorg/build/pull/7#issuecomment-34143909  

Dan,  
  
I can certainly test your changes, or work with you on what's expected behaviour is. The only thing is that I don't have convenient Windows setup, so there will be some delay involved. Hopefully you will be able to bear with me.

---

## Comment 4

> Username: dansmith65  
> Created_at: 2014-02-05 20:22:11 UTC  
> Url: https://github.com/boostorg/build/pull/7#issuecomment-34234763  

Delay isn't really an issue; I'm in no hurry. I'll work on this when I get a chance, then send a pull request.

---
