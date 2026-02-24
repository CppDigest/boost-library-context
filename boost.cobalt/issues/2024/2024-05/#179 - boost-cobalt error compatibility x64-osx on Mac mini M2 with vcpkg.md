# #179 - boost-cobalt error compatibility x64-osx on Mac mini M2 with vcpkg [Open]

> Username: TheLingeringWill  
> Created at: 2024-05-03 14:00:19 UTC  
> Updated at: 2024-12-18 10:20:37 UTC  
> Url: https://github.com/boostorg/cobalt/issues/179  

Using Vcpkg with cmake, I got this error :   
![Screenshot 2024-05-03 at 15 58 07](https://github.com/boostorg/cobalt/assets/156547831/a29ee5ac-b37b-4e1e-a6ab-5f6b74f771ac)

---

## Comment 1

> Username: Tradias  
> Created at: 2024-12-18 09:36:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/179#issuecomment-2550836489  

@klemens-morgenstern  When Boost.Cobalt was first added to vcpkg (Boost 1.84) they set `"supports": "!osx & !ios & !android & !uwp"`. Now that vcpkg is at Boost 1.86 and in the [progress of upgrading to 1.87](https://github.com/microsoft/vcpkg/pull/42678) I wonder if that is still true?  
  
The documentation doesn't say what OSs are supported by Boost.Cobalt: https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#requirements

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-12-18 10:20:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/179#issuecomment-2550934734  

Cobalt doesn't rely on an OS, it just needs a C++20 compliant compiler & asio. I don't know why they set it this way, although I vageuly remember there being some OSX issue. That is however fixed by now, so it should work on OSX, too.
