# #272 - [CMake] Incorrect minimal version of CMake [Closed]

> Username: ldionne  
> Created at: 2016-05-16 19:48:12 UTC  
> Updated at: 2016-05-27 00:33:28 UTC  
> Closed at: 2016-05-27 00:33:28 UTC  
> Url: https://github.com/boostorg/hana/issues/272  

In the `CMakeLists.txt` file, we require a minimum CMake version of 3.0.0 and a minimum version of Boost of 1.59.0. However, CMake 3.0 was released in 2014 and Boost 1.59 was released in 2015, which means that `FindBoost.cmake` won't know about Boost 1.59. We should bump the minimum CMake version to something that knows about Boost 1.59.  
  
Extracted from [this message](http://article.gmane.org/gmane.comp.lib.boost.devel/267232) on the Boost.Dev mailing list.  
  
**Edit:** The first version of CMake that knows about Boost 1.59 appears to be CMake 3.4.0.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-05-16 20:10:03 UTC  
> Url: https://github.com/boostorg/hana/issues/272#issuecomment-219532693  

I think the point of the email was why you write `find_package(Boost 1.59)` instead `find_package(Boost 1.61)`. The `FindBoost.cmake` should be able to find newer versions of boost. If it doesn't then its another reason why boost should move cmake, as a library and build system should not be this coupled together.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-05-16 20:12:05 UTC  
> Url: https://github.com/boostorg/hana/issues/272#issuecomment-219533249  

AFAIU, CMake _will_ find newer versions of Boost. It will simply fail if it can only find < 1.59, which isn't supported.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-05-16 20:12:27 UTC  
> Url: https://github.com/boostorg/hana/issues/272#issuecomment-219533361  

And if I change to 1.61, then I'm saying that 1.59 isn't supported, which isn't the case.

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-05-16 20:18:21 UTC  
> Url: https://github.com/boostorg/hana/issues/272#issuecomment-219534961  

> And if I change to 1.61, then I'm saying that 1.59 isn't supported, which isn't the case.  
  
Yes, of course, so you are saying that 1.59 is the minimal version. It should still find 1.61 as long as `FindBoost.cmake` will say they are compatible.

---

## Comment 5

> Username: ldionne  
> Created at: 2016-05-27 00:33:28 UTC  
> Url: https://github.com/boostorg/hana/issues/272#issuecomment-222031841  

Ok, so the "solution" to this "bug" would be to bump the minimal required version of CMake to 3.4.0, which is the first version that knows about Boost 1.59. However, it would have the undesirable effect of artificially bumping the CMake requirement to something much more recent than what's actually required.   
  
If we keep the requirement to CMake 3.0, and if someone uses a CMake version prior to that, what will simply happen is that Boost will never be found. But since that's an optional requirement anyway, it won't affect the ability to build Hana (only the Fusion/MPL adaptors). So I think the reasonable thing to do is to leave the CMake requirement to 3.0.
