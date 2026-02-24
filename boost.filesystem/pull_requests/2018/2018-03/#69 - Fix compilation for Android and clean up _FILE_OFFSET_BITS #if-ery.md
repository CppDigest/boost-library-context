# #69 Fix compilation for Android and clean up _FILE_OFFSET_BITS #if-ery [Merged]

> Username: alexeikh  
> Created at: 2018-03-03 17:54:12 UTC  
> Updated at: 2018-05-11 08:43:25 UTC  
> Merged at: 2018-05-09 15:14:25 UTC  
> Closed at: 2018-05-09 15:14:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69  

This pull requests cleans up `_FILE_OFFSET_BITS` #if-ery in `operations.cpp`, fixes compilation with new Android NDKs for old Android APIs (issue #65) and, thanks to @Lastique's patch, provides better error checking in `resize_file()` function.  
  
Differences from other pull requests, like #52 and #67:  
  
* Instead of taking easy approach like  
  
```c++  
#if !MY_SETUP  
// I don't care about the mess here.  
#endif   
```  
  
, which may eventually lead to #if-ery like  
  
```c++  
#if !SETUP_3  
#if !SETUP_2  
#if !SETUP_1  
...  
#else  
...  
#endif  
#endif   
#endif   
```  
  
, I am making attempt to clean the mess and provide "flat" #if-ery like  
  
```c++  
#if SETUP_1  
...  
#elif SETUP_2  
...  
#else  
...  
#endif  
```  
  
* During the cleanup, it turns out the either `_FILE_OFFSET_BITS`, or `__USE_FILE_OFFSET64`, or both (!) is always defined, thus it is possible to greatly simplify the #if-ery.  
  
* I am checking if `__ANDROID_API__` is defined. Reason: older Android NDKs, that use non-unified headers, do not define `__ANDROID_API__`.  
  
* Unlike #52, I am including @Lastique's patch for `resize_file()` from #67.  
  
* Unlike #67, I do not define `_FILE_OFFSET_BITS` if compiling with Crystax Android NDK. The reason is that I haven't found any proof that Crystax will somehow provide 64-bit truncate for Android APIs < 21. On the contrary, I found that on Crystax `off_t` is 32-bit: https://github.com/crystax/android-platform-bionic#lp32-abi-bugs.

---

## Comment 1

> Username: rcdailey  
> Created_at: 2018-03-05 15:07:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-370448676  

Thanks for the fixes. Which release of boost will this be available in? Will we have to wait for a 1.67 or can we get it in a hotfix 1.66.1?

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-03-05 16:21:36 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-370474507  

IMO, 1.66.1 is highly unlikely since 1.67 is on the way. I would hope someone with permissions will be able to merge it to develop and then to master before 1.67 is out, but no guarantees on that.

---

## Comment 3

> Username: MaartenBent  
> Created_at: 2018-03-06 10:21:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-370734361  

This doesn't work with toolchains 21, 22 and 23, failing with error:  
```bash  
C:\Android\toolchain21\bin/../lib/gcc/arm-linux-androideabi/4.9.x/../../../../include/c++/4.9.x\cstdio:107:11: error: no member named 'fgetpos' in the global namespace  
  using ::fgetpos;  
        ~~^  
C:\Android\toolchain21\bin/../lib/gcc/arm-linux-androideabi/4.9.x/../../../../include/c++/4.9.x\cstdio:117:11: error: no member named 'fsetpos' in the global namespace  
  using ::fsetpos;  
        ~~^  
2 errors generated.  
```  
Changing the guard to `__ANDROID_API__ < 24` fixes this.  
  
See also [this](https://android.googlesource.com/platform/bionic/+/master/docs/32-bit-abi.md) documentation that mentions API 24 is the first API that has all functions  included in `<stdio.h>`.

---

## Comment 4

> Username: alexeikh  
> Created_at: 2018-03-06 12:44:01 UTC  
> Updated_at: 2018-03-06 23:01:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-370769794  

@MaartenBent, thanks for the heads up.  
  
The issue with `fgetpos` and `fsetpos` seems to be this one: https://github.com/android-ndk/ndk/issues/480 . Android NDK developers fixed it in **r16** with this patch: https://android.googlesource.com/platform/ndk/+/d5acdb0ee5ad536d543b509d83cfa79b9d01b3b1 , **but** only for libc++. I think, they did it as an exception, because compilation failure on just `#include <cstdio>` is really ridiculous. They refuse to fix it for gnustl, because they are deprecating gnustl in favor of libc++.  
  
It means that the issue still exists for Android APIs 21-23 on:  
- NDK r16 and above with gnustl.  
- NDK r14 and r15 with **any STL** and enabled Unified Headers.  
  
@MaartenBent, judging from you error messages, you are using gnustl.  
  
@MaartenBent, I agree, it's better to change the guard to `__ANDROID_API__ < 24`. I will update the pull request.  
  
It's possible to get 64-bit `truncate` for APIs 21-23, but it seems to require some complicated logic. I am not sure it's worth the hassle. And I don't want to further complicate and delay the review of this pull request. If anyone needs 64-bit `truncate` particularly for API range 21-23, I think he/she should make it as a separate pull request.

---

## Comment 5

> Username: Bjoe  
> Created_at: 2018-03-07 13:08:04 UTC  
> Updated_at: 2018-03-07 13:08:29 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-371132690  

It compiles with android API level 19 and the verification if the file is not to big for resizing does indeed looks like a good fix, so I can execute my App on an Android with a higher API level (with 64-Bit support).  
  
With the last change, is this really sure that this is compiling with gnustl and API level 24? And when I check the [Roadmap](https://android.googlesource.com/platform/ndk/+/master/docs/Roadmap.md) from Android they planned:  
> NDK r17  
> Estimated release: Q1 2018  
> Default to libc++  
>  
> NDK r18  
> Estimated release: Q2 2018  
> Remove non-libc++ STLs  
> Remove GCC  
>  
So Q2/2018 it's not far away... gnustl will be removed.  
Maybe you add another check which STL libraries is used like  
`(__ANDROID_API__ < 21 && _LIBCPP_VERSION) || (__ANDROID_API__ < 24 && __GLIBCXX__)`  
or something like that. See also [Pre-defined C/C++ Compiler Macros](https://sourceforge.net/p/predef/wiki/Libraries/)  
Btw ... should I close #65 ?

---

## Comment 6

> Username: MaartenBent  
> Created_at: 2018-03-07 13:33:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-371139251  

> With the last change, is this really sure that this is compiling with gnustl and API level 24  
  
For me it compiles successfully with API 21 and API 24. I do not define the stl when creating the [toolchain](https://developer.android.com/ndk/guides/standalone_toolchain.html#creating_the_toolchain) so gnustl is used as default.  
  
> Maybe you add another check which STL libraries is used like  
  
This doesn't work because `__GLIBCXX__` is not yet defined, from your link:  
  
> One of the standard header files must be included before any of the following macros are defined.  
  
After moving `#include <cstdlib>` to the beginning of the file it works. I did not check if `_LIBCPP_VERSION` is defined correctly when using libc++, or if moving the include has any side effects.

---

## Comment 7

> Username: Lastique  
> Created_at: 2018-03-07 13:42:51 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-371141736  

After you included a system header, it's already too late to define `_FILE_OFFSET_BITS`.

---

## Comment 8

> Username: alexeikh  
> Created_at: 2018-03-14 16:56:57 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-373096400  

> Btw ... should I close #65 ?  
  
@Bjoe: No, of course not. First, you have very good description of the issue. With relevant error messages and code snippets. Second, if you close the issue - it may confuse people, someone may get an idea that the issue is fixed.

---

## Comment 9

> Username: alexeikh  
> Created_at: 2018-03-14 17:07:27 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-373100137  

Dear Boost.Filesystem maintainers, @Beman and @pdimov.  
  
I submitted this pull request several days ago. How can I get it reviewed?

---

## Comment 10

> Username: alexeikh  
> Created_at: 2018-04-12 11:42:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-380776305  

Congratulations with the International Space Day, everyone. According to the Git logs, @Beman was on Earth 2 days ago and made some commits to the repository.  
  
On this nice day, can I ask @Beman for some attention to this pull request?

---

## Comment 11

> Username: alexeikh  
> Created_at: 2018-05-03 18:16:58 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-386388926  

This pull request is waiting for review 2 months already.  
  
@Beman , @pdimov : Boost 1.67 is out, maybe now you have time to review and merge the patch?  
  
Maybe you see some issue in the patch? And have suggestion for improvement?

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-05-03 18:25:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-386391623  

Looks good to me. If @Beman doesn't object, I'll merge that in a few days.

---

## Comment 13

> Username: alexeikh  
> Created_at: 2018-05-11 08:20:03 UTC  
> Updated_at: 2018-05-11 08:43:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/69#issuecomment-388295819  

Hurrah! :) Thanks for the merging!

---
