# #260 Add tests requirements [Merged]

> Username: Kojoley  
> Created at: 2019-03-18 02:06:26 UTC  
> Updated at: 2019-03-29 20:26:17 UTC  
> Merged at: 2019-03-18 18:12:07 UTC  
> Closed at: 2019-03-18 18:12:07 UTC  
> Url: https://github.com/boostorg/gil/pull/260  

Not a full list of used features, but it should be enough. Will wash away failures on unsupported compilers from the regression board.  
  
### References  
  
- #149

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2019-03-18 09:58:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/260#pullrequestreview-215504457  

LGTM. Thanks!

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-03-18 18:12:31 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474038491  

@stefanseefeld Shall we target this for our Boost 1.70 milestone?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-03-18 18:14:49 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474039417  

Sidenote: Your jamfiles have `<cxxflags>-std=c++11`, I strongly suggest to remove it.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2019-03-18 18:20:50 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474041758  

@Kojoley how would you suggest we express requirements ?  
Coincidentally, I just ran into an issue with the `Boost.uBLAS` project, where part of the library requires C++17. So we ended up adding requirements to the concerned tests, examples, and benchmarks, using `<cxxstd>17`. It turned out that that isn't quite working, as some testers set compiler flags using `<cxxflags>`, so `<cxxstd>` wasn't used in those cases.  
So I'm genuinely curious about what the right way to express language version requirements is.

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2019-03-18 18:23:03 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474042608  

@mloskot even though I would like to add this (as well as some <cxxstd> / <cxxflags> cleanup) into master, I'm afraid that this could trigger some failures, and thus affect the release schedule. It doesn't have any effect on the (user-visible) code, so I don't think it's urgent.

---

## Comment 6

> Username: mloskot  
> Created_at: 2019-03-18 18:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474044092  

@Kojoley Yes, I'm aware and @pdimov was pointing it out that our Jamfile needs clean-up (e.g. #149)  
  
@stefanseefeld The thing is, AFAIU, this PR actually fixes a known issue for the Boost regression matrix (#149)

---

## Comment 7

> Username: Kojoley  
> Created_at: 2019-03-18 18:36:29 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474047620  

You should not force compiler standard flags from the tests, it will prevent you from testing higher standard versions, just skip the test via requirements or do nothing based on Boost.Config macros.  
  
The `config.requires` returns `<build>no` when feature check fails, so you can use it everywhere where you could supply `<build>no`. Per test example https://github.com/boostorg/fusion/blob/4c51811315af8257f420ada0f195a89d479c684e/test/Jamfile#L231-L237

---

## Comment 8

> Username: mloskot  
> Created_at: 2019-03-18 18:50:10 UTC  
> Updated_at: 2019-03-18 18:50:30 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474053061  

> You should not force compiler standard flags from the tests, it will prevent you from testing higher standard versions  
  
Yes, the idea is clear and I also try to achieve it in the CMake config, https://github.com/boostorg/gil/blob/develop/CMakeLists.txt#L26 and I planned to explore it further to get clear about exactly the same question as @stefanseefeld  is asking earlier.  
  
Here is what I've learned so far:  
  
1. use the `requires` to filter unsupported compilers  
2. do not add `<cxxflags>-std=c++11`  to allow newer versions  
3. call `b2` with explicit `cxxstd=V` where `V` lists  one or more from [11 1y 14 1z 17 2a latest](https://github.com/boostorg/build/blob/b062bf5eeeecae87c081319ebf7a2c03510b5a34/src/tools/features/cxxstd-feature.jam#L23-L25)  
  
But, is 1. and 2. enough to express minimum required version?

---

## Comment 9

> Username: Kojoley  
> Created_at: 2019-03-18 19:25:12 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474065342  

> But, is 1. and 2. enough to express minimum required version?  
  
In most cases the things Config and Predef provide should be enough. You can always write a check that will satisfy your particular requirements.  
  
> I also try to achieve it in the CMake config, /CMakeLists.txt@develop#L26   
  
I am not an expert in CMake, but seems you are doing it wrong. It is the same thing as just setting `-std` manually. The minimum standard requirement for the target seems should be expressed like [this](https://cmake.org/cmake/help/v3.14/manual/cmake-compile-features.7.html#requiring-language-standards). CMake also allows you to make [future checks](  
https://cmake.org/cmake/help/v3.14/manual/cmake-compile-features.7.html#optional-compile-features), and have build-in [standard future checks](https://cmake.org/cmake/help/v3.14/manual/cmake-compile-features.7.html#compile-feature-requirements).

---

## Comment 10

> Username: mloskot  
> Created_at: 2019-03-18 20:39:02 UTC  
> Updated_at: 2019-03-18 20:39:49 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474091425  

> In most cases the things Config and Predef provide should be enough.  
  
Good.  
  
> You can always write a check that will satisfy your particular requirements.  
  
I do not wish to write any custom checks, if that is what you suggest.  
  
The goal of this build configuration is as simple as this:  
1. Force any pre C++11 compiler to fail, earlier than during actual compilation.  
2. Behave nicely when we are being tested as part of Boost super-project tree.  
  
All other aspects, if any, are low priority. If, however, anyone feels anything else should be improved, great, then PRs are welcome.  
  
> I am not an expert in CMake, but seems you are doing it wrong.  
  
I disagree. I would have been doing it wrong if there were public targets defined for use by GIL users.  
  
> It is the same thing as just setting -std manually.   
  
Yes, it is and there is nothing wrong with that there.  
One can build tests with `CMAKE_CXX_STANDARD=11` as the default or request `CMAKE_CXX_STANDARD=17` or later.  
  
GIL's CMake config is not a pure modern CMake, yet. Regardless if it is modern or not, it is dedicated to those who wish build GIL tests and examples, and this config is not supposed to export anything to public or be well-behave interacting with any external CMake config. Shortly, it is purely for internal development purposes. Setting `CXX_STANDARD` globally is a convenient shortcut.

---

## Comment 11

> Username: Kojoley  
> Created_at: 2019-03-18 21:37:05 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474111375  

> I do not wish to write any custom checks, if that is what you suggest.  
  
Actually I am not even sure what was your question, so the answer was broad.  
  
> > It is the same thing as just setting -std manually.  
>  
> Yes, it is and there is nothing wrong with that there.  
  
Just remember that by not respecting platform defaults you are making troubles for package maintainers.

---

## Comment 12

> Username: mloskot  
> Created_at: 2019-03-19 07:43:55 UTC  
> Updated_at: 2019-03-19 10:12:01 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474231682  

Your points are clear and valid, in general. They just do not apply to GIL (not to its current setup; until we've got Boost official CMake, etc.)  
There's no Jamfile or CMakeLists.txt here to package.

---

## Comment 13

> Username: mloskot  
> Created_at: 2019-03-20 08:15:00 UTC  
> Url: https://github.com/boostorg/gil/pull/260#issuecomment-474729662  

@Kojoley I should have clarified my point better. The current build configurations in GIL are for development purposes, for convenience of contributors. They are not free from hacks and they could follow best practices more. It's just that 1) it has not been my priority to improve it; 2) I have failed myself to see where or how to improve it (I know the CMake deserves modernisation though). I do not claim the current build configurations are optimal.  
  
If you know where to improve the build configs and if you are willing to help out, awesome, **please do**!

---
