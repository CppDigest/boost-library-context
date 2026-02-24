# #500 Remove Travis [Closed]

> Username: mborland  
> Created at: 2021-01-24 11:25:43 UTC  
> Updated at: 2024-02-22 10:26:19 UTC  
> Closed at: 2024-02-22 10:26:19 UTC  
> Url: https://github.com/boostorg/math/pull/500  

This PR is built as a discussion point based off @jzmaddock post to the mailing list. It is a modified version of the Travis config to remove all the C++03 testing and add tests on `ppc64le`, `s390x`, and `arm64` architectures. Before this actually gets sent through Travis (architectures are still in beta) what do we actually want to test? The expanded scope of the Github actions runs with all the additional architectures? Additionally what should trigger a run? I expect that this run will take days to get results back from. Adding @ckormanyos too since he's doing the same thing over at multiprecision.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-01-24 11:44:58 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-766334388  

> doing the same thing over at multiprecision.  
  
Thanks @mborland for kicking off this exciting extension of technology. Multiprecision is a bit behind math on GHA, so we need to catch up in the first place. Assuming that gets done, and given the historically synergy between math and multiprecision (also on this matter), we'll try to keep math/multiprecision synchronized with each other regarding the technical advances in this PR.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-01-24 13:05:42 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-766344929  

Thanks Matt!  
  
My original thought on this was to test ONE compiler (platform default gcc probably) on each architecture.  That would bring things down to a manageable level, and would let us see how long the build takes.  Plus we have compiler-compatibility testing well covered already.  The only question then is whether to use the default linux image or the latest and greatest.  The issue with the latter is it goes out of date, the issue with the former is it gets changed without notice :(

---

## Comment 3

> Username: mborland  
> Created_at: 2021-01-24 13:28:56 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-766348226  

Pinning to the most recent LTS Ubuntu would give you 20.04 with GCC9, but it would be hard-coded in the file. It would only need updated about every two years which isn't a terrible amount of work. The latest would automatically update and currently be 20.10 and GCC10. I think GCC10 is a good starting point because even distros that are slow to adopt new features like RHEL provide GCC10.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-01-24 14:06:46 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-766353799  

I went with Focal (20.04) as the distribution since the default according to the Travis docs is Xenial (16.04) with GCC 5.4. That seemed pretty old on both accounts.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-01-24 14:09:52 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-766354317  

@mborland Matt, when does one decide to pin an Ubuntu version and its default GCC or go with Focal (which I assume is latest?) and its native GCC? What guides these choices?

---

## Review 6 [Commented]

> Username: mborland  
> Created_at: 2021-01-24 14:20:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/500#pullrequestreview-574935026  

📁 .travis.yml

```diff
  12 |-   - linux
  13 |-   - osx
  10 |+ dist: focal
```

> Username: mborland  
> Created_at: 2021-01-24 14:20:51 UTC  
> Updated_at: 2021-01-31 19:03:58 UTC  
> Url: https://github.com/boostorg/math/pull/500#discussion_r563299650  

@ckormanyos Here is where you would plug in the distro that you want to use. The [Travis docs](https://docs.travis-ci.com/user/reference/linux) show the versions of the default tools in the toolchain by OS. Focal is the latest LTS version of Ubuntu.


---

## Comment 7

> Username: mborland  
> Created_at: 2021-01-31 19:05:04 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-770432474  

@jzmaddock Now this PR is solely to remove Travis since it is of no use anymore.

---

## Comment 8

> Username: mborland  
> Created_at: 2021-02-01 16:00:30 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-770962476  

@jzmaddock [I was able to run ](https://travis-ci.org/github/mborland/math/builds/756971703)gcc with -std=c++17 on multiple architectures using Travis. In lieu of having something in the repo I could periodically merge origin develop into my fork's develop to test and report back with results.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2021-02-01 18:34:52 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-771067112  

Thanks Matt!  
  
The run was a bit of a bloodbath though wasn't it?  
  
OK, things to do:  
  
* I notice some timeouts - I also have some for the drone CI PR, so I'm in the process of splitting things up a little more which will hopefully help.  
* Most of the errors look to be long double related, but I don't pretend to have had an exhaustive look through!  I think we need some more information, the output from libs/config/test/math_info.cpp would help, I'll either add a target to Boost.Config so we can run it like we do config_info.cpp, or else maybe refactor the 2 files into one.  
* We have a small `long double` test case in libs/math/test/test_long_double_support.cpp which is part of the "misc" test suite.... OK, the result is missing from ppc64, ARM64 has mild failures (in std::exp) which will probably cause a lot of failures downstream in our code (but *might* just be a case of altering "expected" error rates).  s390 has a similar minor failure in that test.  
  
There sure is a lot to do here!

---

## Comment 10

> Username: mborland  
> Created_at: 2021-02-02 17:25:51 UTC  
> Url: https://github.com/boostorg/math/pull/500#issuecomment-771815396  

> Thanks Matt!  
>   
> The run was a bit of a bloodbath though wasn't it?  
  
I would not have guessed that it was going to be this run. I understand your concern with other architectures now.  
  
> OK, things to do:  
>   
>     * I notice some timeouts - I also have some for the drone CI PR, so I'm in the process of splitting things up a little more which will hopefully help.  
  
I will keep an eye out for that. The only one that looks like it fully timed out ran for 50 mins. Not sure why some of them were unresponsive for 10mins or more.  
  
>     * Most of the errors look to be long double related, but I don't pretend to have had an exhaustive look through!  I think we need some more information, the output from libs/config/test/math_info.cpp would help, I'll either add a target to Boost.Config so we can run it like we do config_info.cpp, or else maybe refactor the 2 files into one.  
>  
>     * We have a small `long double` test case in libs/math/test/test_long_double_support.cpp which is part of the "misc" test suite.... OK, the result is missing from ppc64, ARM64 has mild failures (in std::exp) which will probably cause a lot of failures downstream in our code (but _might_ just be a case of altering "expected" error rates).  s390 has a similar minor failure in that test.  
  
Lots of long double in general and __float128 related failures on ARM64 and S390. Those two seem to have very similar failures across the board.   
  
>   
> There sure is a lot to do here!  
  
I tried to consolidate into a palatable number of issues. I think a fair number can be "solved" by adjusting error tolerance.

---
