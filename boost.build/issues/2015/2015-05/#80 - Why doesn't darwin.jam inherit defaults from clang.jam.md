# #80 - Why doesn't darwin.jam inherit defaults from clang.jam? [Open]

> Username: mnbittorrent  
> Created at: 2015-05-26 21:27:40 UTC  
> Updated at: 2015-05-27 15:18:41 UTC  
> Url: https://github.com/boostorg/build/issues/80  

In darwin.jam, it looks like gcc.jam is inherited instead of clang.jam. I'm not sure why this is the case since Apple basically replaces gcc with clang on Mac OS X. This means that build flags are not inherited like I would expect.

---

## Comment 1

> Username: vprus  
> Created at: 2015-05-27 06:23:28 UTC  
> Url: https://github.com/boostorg/build/issues/80#issuecomment-105772574  

Mike,  
  
darwin.jam was created when GCC was the default and only compiler. Clang is  
supposed to be mostly option-compatible, what flags do you find missing or  
broken?  
  
Thanks,  
Volodya  
  
On Ср, 27 мая 2015 at 0:27 Mike Naquin notifications@github.com wrote:  
  
> In darwin.jam, it looks like gcc.jam is inherited instead of clang.jam.  
> I'm not sure why this is the case since Apple basically replaces gcc with  
> clang on Mac OS X. This means that build flags are not inherited like I  
> would expect.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/build/issues/80.

---

## Comment 2

> Username: mnbittorrent  
> Created at: 2015-05-27 15:18:40 UTC  
> Url: https://github.com/boostorg/build/issues/80#issuecomment-105953179  

clang 3.6 generates a warning with the -finline-functions flag. This has been fixed in clang-darwin.jam, but since darwin.jam doesn't inherit from clang-darwin.jam, it doesn't get the fix.  
  
We configure our toolchain with something like this:  
  
```  
using darwin : iphoneos : clang++ :  
        <striper> <root>"$(XCODE_PATH)/Platforms/iPhoneOS.platform/Developer"  
        <compileflags>-Werror  
        <compileflags>-miphoneos-version-min=8.0  
        <compileflags>"-arch armv7"  
        <compileflags>"-arch armv7s"  
        <compileflags>"-arch arm64"  
        <compileflags>"-isysroot $(XCODE_PATH)/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk"  
        <cxxflags>-std=c++1y  
        <cxxflags>-stdlib=libc++  
    : <architecture>arm <target-os>iphone  
;  
```  
  
Then call `b2 toolset=darwin-iphoneos architecture=arm target-os=iphone release`.
