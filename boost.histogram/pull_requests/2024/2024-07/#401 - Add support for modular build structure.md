# #401 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:34:13 UTC  
> Updated at: 2025-06-05 17:08:12 UTC  
> Merged at: 2025-06-05 13:23:07 UTC  
> Closed at: 2025-06-05 13:23:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/401  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:06:09 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2295312013  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2025-04-18 08:09:55 UTC  
> Updated_at: 2025-04-18 08:11:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2814881965  

I didn't see this, because I am not getting notifications when a PR is opened unless someone mentions me with @HDembinski. I changed something in the options of this repo, putting my email address in explicitly under email notifications. Perhaps that is what was missing.  
  
@grafikrobot Thank you for the changes to the build. There seems to be an issue with boost.random in the slow tests, a linker error. Do you have an idea how to fix this? You also remove some extra warning flags `cxxflags=-Wno-unused-parameter cxxflags=-Wno-uninitialized`, why?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2025-04-23 14:04:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2824426945  

> You also remove some extra warning flags `cxxflags=-Wno-unused-parameter cxxflags=-Wno-uninitialized`, why?  
  
Those are for avoiding warnings in Math (IIRC) that cause errors because of the warnings-as-errors option.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2025-04-30 01:55:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2840613725  

@HDembinski   
  
> There seems to be an issue with boost.random in the slow tests, a linker error. Do you have an idea how to fix this?  
  
Have those slow tests ever passed? Asking as I don't see them in previous commits.

---

## Comment 5

> Username: HDembinski  
> Created_at: 2025-04-30 11:42:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2841709926  

> @HDembinski  
>   
> > There seems to be an issue with boost.random in the slow tests, a linker error. Do you have an idea how to fix this?  
>   
> Have those slow tests ever passed? Asking as I don't see them in previous commits.  
  
Yes, they used to pass. I only run them on PRs, but not on pushes, that's why they don't show up in the list of commits.

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2025-05-09 13:29:04 UTC  
> Updated_at: 2025-05-09 13:33:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2866553457  

@HDembinski FYI.. I've been trying to resolve the CI errors. But I can't make progress in most of them myself.  
I'm still investigating the appleclang missing link symbol (made harder as I don't have a macos machine to test with). What do you want to do about these..?  
  
- The coverage fails after doing the building. I'm not familiar with the coverage it's doing to decipher why the lcov file is missing.  
- The slow clang build detects ODR violations.  
- The slow gcc test detects dangling temp reference error/warning in the main test.  
  
PS. I do notice that the coverage and slow tests have not been working for PRs for some time. Including for this one that I made to investigate the build differences between unchanged develop and my changes: https://github.com/boostorg/histogram/pull/409

---

## Comment 7

> Username: HDembinski  
> Created_at: 2025-05-14 11:49:16 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2879903276  

@grafikrobot Thank you for the work, I will look into this on the weekend. The problem for me also is that I am not developing on a Mac anymore, I have a Windows machine now (urgh). I think I can fix all these three issues.

---

## Review 8 [Commented]

> Username: HDembinski  
> Created_at: 2025-05-17 14:39:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/401#pullrequestreview-2848300844  

📁 build.jam

```diff
  19 |+     /boost/serialization//boost_serialization
  20 |+     /boost/throw_exception//boost_throw_exception
  21 |+     /boost/variant2//boost_variant2 ;
```

> Username: HDembinski  
> Created_at: 2025-05-17 14:39:32 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#discussion_r2094139302  

@grafikrobot This is causing issues for `test//minimal`, which is why the appleclang test fails. boost_serialization is an optional dependency for boost-histogram. boost-serialization does not work with exception-handling=off, so it cannot be a constant dependency that is always included. The test//minimal tests avoid all the heavy dependencies like boost-serialization that themselves pull in tons of other dependencies.


---

## Comment 9

> Username: HDembinski  
> Created_at: 2025-05-17 15:10:13 UTC  
> Updated_at: 2025-05-17 15:11:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2888447506  

So, I finally had a closer look.  
  
>     * The coverage fails after doing the building. I'm not familiar with the coverage it's doing to decipher why the lcov file is missing.  
  
It looks like `../../b2 $B2_OPTS toolset=gcc-${GCC_VERSION} cxxstd=latest coverage=on test//all` doesn't compile anything. No tests are run and no coverage data is created. Any idea why? The alias test//all should run all tests.  
  
If I remember correctly, lcov only works with a matching version of gcc. If you know a better way to measure coverage in C++, I'd love to switch to another tool. You upgraded from gcc-11 to gcc-15, I will probably need to upgrade lcov as well.  
  
>     * The slow clang build detects ODR violations.  
  
That's because of boost-serialization if you look closely, which you added as a constant dependency to this build. boost-serialization causes lots of issues, that's why I keep it as an optional dependency that is only compiled for some tests that are not as strict. I run harder tests on boost-histogram itself.  
   
>     * The slow gcc test detects dangling temp reference error/warning in the main test.  
  
That's a newly discovered bug - it seems.

---

## Comment 10

> Username: HDembinski  
> Created_at: 2025-05-17 15:33:34 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2888460609  

@grafikrobot Progress.  
  
The coverage test still fails, because it doesn't compile anything. test//all works in other circumstances. Weird.  
  
The gcc slow test still fails because of that "possibly dangling pointer". As far as I understand, the pointer shouldn't be dangling because of NRVO. The issue is not detected by another other compiler and not caught by the UBSAN / ASAN tests, so perhaps a false positive. As a workaround, we could turn off that warning for gcc for that line.

---

## Review 11 [Commented]

> Username: grafikrobot  
> Created_at: 2025-05-17 15:46:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/401#pullrequestreview-2848335798  

📁 CMakeLists.txt

```diff
  17 |     Boost::config
  18 |     Boost::core
  19 |+     Boost::math
```

> Username: grafikrobot  
> Created_at: 2025-05-17 15:46:07 UTC  
> Updated_at: 2025-05-17 15:46:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#discussion_r2094152569  

Should you remove that to mirror the b2 side where math is optional?

> Username: HDembinski  
> Created_at: 2025-05-17 15:58:44 UTC  
> Updated_at: 2025-05-19 07:24:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#discussion_r2094155141  

Maybe, but it doesn't cause so many problems, because it is header only.

> Username: grafikrobot  
> Created_at: 2025-05-18 13:31:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#discussion_r2094525496  

I failed to parse that sentence. :-( Just saying the it's preferred to be consistent between the two build systems.

> Username: HDembinski  
> Created_at: 2025-05-19 07:26:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#discussion_r2095022877  

Sorry, forgot a negation there.  
  
boost-serialization is a pain because it is a compiled library. If it is a global dependency, it always gets compiled even if it is not even used in the tests that are actually run. boost-math is header-only, so if the tests that use it are not compiled, it does not cause any issues.

> Username: HDembinski  
> Created_at: 2025-05-19 07:27:51 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#discussion_r2095024730  

I agree with your point regarding consistency.


---

## Comment 12

> Username: grafikrobot  
> Created_at: 2025-05-18 14:36:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2889026332  

> The coverage test still fails, because it doesn't compile anything. test//all works in other circumstances. Weird.  
  
I can't repro that locally, with the same compiler. So definitely strange why in CI it would not build the tests in this case.

---

## Comment 13

> Username: grafikrobot  
> Created_at: 2025-05-19 13:20:29 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2891014512  

@HDembinski FYI.. I'm now thinking there's a bug in B2 parsing the CLI arguments in this particular instance. As it's behaving as if the test//all, or test//quick, argument isn't there.

---

## Comment 14

> Username: HDembinski  
> Created_at: 2025-05-20 16:52:43 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2895192764  

Bizarr. I haven't tried to reproduce locally, but the same issue should show up then if I use the same args. Will try this.

---

## Comment 15

> Username: HDembinski  
> Created_at: 2025-06-05 12:26:42 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2944038213  

@grafikrobot Everything fixed now. I will do some finishing touches, then merge.

---

## Comment 16

> Username: grafikrobot  
> Created_at: 2025-06-05 14:42:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2944842709  

@HDembinski  many, many, thanks for figuring out the build problems to get this merged!

---

## Comment 17

> Username: HDembinski  
> Created_at: 2025-06-05 17:08:11 UTC  
> Url: https://github.com/boostorg/histogram/pull/401#issuecomment-2945315278  

Thanks for all the help!

---
