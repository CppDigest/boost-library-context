# #9 Fix gcc 6 warnings about invoking placement new on a buffer of insufficient size [Merged]

> Username: Lastique  
> Created at: 2016-04-02 12:07:12 UTC  
> Updated at: 2016-07-11 22:46:25 UTC  
> Merged at: 2016-07-11 22:24:49 UTC  
> Closed at: 2016-07-11 22:24:49 UTC  
> Url: https://github.com/boostorg/function/pull/9  



---

## Comment 1

> Username: eldiener  
> Created_at: 2016-04-22 11:00:12 UTC  
> Url: https://github.com/boostorg/function/pull/9#issuecomment-213379307  

According to gcc, no official release of gcc6 has occurred yet. So I cannot see how I can test this against gcc6 until a release of gcc6 has been created and is available for an OS I run. I can test for gcc-5.3 and below as well as clang, VC++ etc but I would not be able to determine if this fixes an issue in gcc6.

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-04-22 11:56:47 UTC  
> Url: https://github.com/boostorg/function/pull/9#issuecomment-213393852  

According to https://gcc.gnu.org/ gcc 6.1 has been released on 2016-04-15. On boost develop test page there are Flast-FreeBSD10-gcc-6.0.0~gnu++11 and trippels-powerpc64le-gcc-6.0 testers, which use gcc 6 and show the warnings e.g. here: http://www.boost.org/development/tests/develop/output/trippels-powerpc64le-gcc-6-0-log-gcc-6-0-1-warnings.html#filt_matches_spirit_classic.  
  
I now realize that trippels shows version 6.0.1, which is not the final gcc 6 version (6.1). So I guess I didn't see the warnings on the final gcc 6 yet, but I'm fairly sure they will be present there.

---

## Comment 3

> Username: mclow  
> Created_at: 2016-04-22 16:30:52 UTC  
> Updated_at: 2016-04-22 16:31:03 UTC  
> Url: https://github.com/boostorg/function/pull/9#issuecomment-213500137  

I just followed the gcc 6 link on https://gcc.gnu.org/, and it says (at https://gcc.gnu.org/gcc-6/) "As of this time no releases of GCC 6 have yet been made."  My expectation is that release is imminent.

---

## Comment 4

> Username: eldiener  
> Created_at: 2016-04-24 14:26:48 UTC  
> Url: https://github.com/boostorg/function/pull/9#issuecomment-213969288  

Fedora 24 is the only Linux distro which I have found and which I run that will have gcc6 upon its release as the default version of gcc. Fedora24 will be available in June. I have not found any information when mingw-64 on Windows will have a gcc6 version. I understand that it might be possible to build gcc6 now from source but I am loth to try this myself. I will be glad to test your fix for gcc6 when I can do it with a released version.

---

## Comment 5

> Username: Romain-Geissler-1A  
> Created_at: 2016-05-30 16:16:36 UTC  
> Url: https://github.com/boostorg/function/pull/9#issuecomment-222522700  

Hi,  
  
Has this change been reviewed already ? I will apply it locally on my local copy of Boost but I would feel better if this pull request was accepted and merged into the official repo ;)  
  
Cheers,  
Romain

---

## Comment 6

> Username: Lastique  
> Created_at: 2016-07-11 11:14:58 UTC  
> Url: https://github.com/boostorg/function/pull/9#issuecomment-231706824  

There are currently multiple gcc 6 testers in the test matrix. Could someone apply this PR so that it at least receives regular testing?

---
