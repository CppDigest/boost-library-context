# #28 - Combined builds for iOS [Closed]

> Username: Orphis  
> Created at: 2016-04-06 17:50:24 UTC  
> Updated at: 2016-04-08 21:21:10 UTC  
> Closed at: 2016-04-06 17:59:54 UTC  
> Url: https://github.com/boostorg/context/issues/28  

It is possible to use combined builds on OSX and have a really simple build process but it isn't on iOS where we need to rebuild the library n times and lipo it. It's really inconvenient!  
So, what do you think about having a similar file for iOS and use the preprocessor to automatically use the right implementation for the processor?  
  
Also, out of curiosity, why don't you maintain such a file to handle the tier1 platforms and compilers and automatically pick the right implementation?

---

## Comment 1

> Username: olk  
> Created at: 2016-04-06 17:59:54 UTC  
> Url: https://github.com/boostorg/context/issues/28#issuecomment-206489873  

I don't use iOS/OSX / I've never owned a Mac - so it's not my target platform (I'm not familiar with it) I've no possibility to develop/test code for iOS/OSX.  
But you are welcome to provide patches.

---

## Comment 2

> Username: Orphis  
> Created at: 2016-04-07 13:15:28 UTC  
> Url: https://github.com/boostorg/context/issues/28#issuecomment-206894858  

So I guess you're not opposed to the idea then of having a combined file to simplify the build process. Great. I'll try to prepare a patch to do that.

---

## Comment 3

> Username: olk  
> Created at: 2016-04-07 13:31:54 UTC  
> Url: https://github.com/boostorg/context/issues/28#issuecomment-206904972  

please use branch develop - note that jump_combined_sysv_macho_gas.S etc. is already available

---

## Comment 4

> Username: Orphis  
> Created at: 2016-04-08 21:21:10 UTC  
> Url: https://github.com/boostorg/context/issues/28#issuecomment-207610686  

Will do!  
Yes, I know that one exists, which is why I proposed to do the same for iOS. There's no issue building a universal library on OSX with that, so it should just work on iOS too and simplify the build a lot. Just specify the arch and then the preprocessor will pick the right implementation! (in theory...)  
  
In theory, it should be possible to even combine OSX and iOS together in one file as Apple provides headers with macros to detect the current platform and SDK you are using to either target OSX, iOS (real device) or iOS (simulator).
