# #291 GitHub Actions and YAML script [Merged]

> Username: ckormanyos  
> Created at: 2021-01-18 14:09:20 UTC  
> Updated at: 2021-02-04 12:42:29 UTC  
> Merged at: 2021-02-04 11:27:07 UTC  
> Closed at: 2021-02-04 11:27:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291  

This draft pull request starts off with a YAML script which performs an echo of _hello world_.  
This draft PR is intended to identify interest in putting the multiprecision CI on GitHub Actions in YAML.  
  
I can handle the YAML code hopefully myself based on the work in [Math 476](https://github.com/boostorg/math/pull/476)  
  
Is this a desired feature?  
Anything that needs to be specially accounted for?

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-01-18 15:22:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-762316025  

Thanks Chris!  
  
Something that I've been meaning to bring up, is that now that we also have a dedicated drone CI service for Boost (albeit currently broken by changes to Boost.Build :(  ), we should probably try to find a way to balance the load between github and drone and then retire travis and maybe appveyor too?

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-01-18 22:03:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-762489396  

This draft PR is inspired by the work in math... And it seems like a few tests run with essentially the same cool YAAML code...  
  
There are some questions I have about the YAML code.  
  
@mborland your work with YAML in math inspired this draft PR and I was wondering if you might help with a question or two...?  
  
How do you feel is the best way to verify success or detect failure in one of the given jobs? Consider [this line running b2](https://github.com/boostorg/multiprecision/blob/5047234e76f088abfab7b2e005a0ae12a1a47347/.github/workflows/multiprecision.yml#L55). How do we know that the return value of b2 is zero on pass/fail and how does that get returned by the shell in a detectable way by the YAML code?

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-01-19 07:37:00 UTC  
> Updated_at: 2021-01-19 07:37:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-762659683  

> Something that I've been meaning to bring up, is that now that we also have a dedicated drone CI service for Boost..., we should probably try to find a way to balance the load between github and drone and then retire travis and maybe appveyor too?  
  
This seems like a good idea. Another variation might be to run CI in GitHub actions for push/merge, but reserve the full integration run on the drone for PRs. One key element if this division is sought is to ensure that the information retrieved from each build agent is correct and that they are consistent with each other.  
  
I am ever so slowly looking into this, maybe with some help as we progress...

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-01-19 12:52:26 UTC  
> Updated_at: 2021-01-19 12:54:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-762821661  

There is forward motion on this draft PR.  
  
Today I have added some builds on a Win pipeline. A brief summary of the GitHub actions on math shows us that it is divided into three main classes of jobs: 1) g++ on Ubuntu, 2) CLang on MacOS, 3) MSVC/MinGW on Win.  
  
In the multiprecision GitActions in the draft PR, we now have a subset of tests running on 1 and 3  
  
In this draft PR, the GitHub actions are triggered on the event _push_. Furthermore, the GitHub Actions in this draft PR do not use a _CI SKIP_ filter. So when you  say, [CI SKIP], the GitHub Actions on this branch _WILL_ run, but Boost's normal Drone CI, however, will not run. In this way, the GitHub Actions on a given Multiprecision branch can be tested independently of running the _official_ Boost.Multiprecision CI.  
  
I am still not entirely sure if the YAML is actually assessing the return value from b2. This must be verified.  
  
Hi @jzmaddock, this thing is far enough along that you might take a look and if you merge ./.github/workflows/multiprecision.yml to your big bad C++11 PR, you could try some GitHub actions there if you like.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-01-19 13:02:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-762826343  

>Hi @jzmaddock, this thing is far enough along that you might take a look and if you merge ./.github/workflows/multiprecision.yml to your big bad C++11 PR, you could try some GitHub actions there if you like.  
  
Done!

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-01-19 15:35:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-762921963  

>> take a look and if you merge ./.github/workflows  
  
> Done!  
  
Thank you. Great!  
  
I will add a few more test suites, language standards and OS/Compiler systems ASAP and see how they run. Then i will report back in this draft PR.

---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2021-01-19 15:36:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/291#pullrequestreview-571369388  

📁 .github/workflows/multiprecision.yml

```diff
  53 |+         working-directory: ../boost-root/libs/config/test
  54 |+       - name: Test
  55 |+         run: ../../../b2 -j3 toolset=$TOOLSET ${{ matrix.suite }} define=CI_SUPPRESS_KNOWN_ISSUES define=SLOW_COMPILER
```

> Username: jzmaddock  
> Created_at: 2021-01-19 15:36:59 UTC  
> Updated_at: 2021-02-04 07:19:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#discussion_r560269670  

These are running sufficiently fast now, that we *might* not need the SLOW_COMPILER define any more - it was there to work around some time-outs.  
  
Given time, we should also see which CI_SUPPRESS_KNOWN_ISSUES conditional compiles we can remove - most relate to old compilers which will no longer be tested anyway.

> Username: mborland  
> Created_at: 2021-01-19 15:57:11 UTC  
> Updated_at: 2021-02-04 07:19:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#discussion_r560285410  

Each job in a workflow is limited to 6 hours of run time and the entire workflow is limited to 72 hours. You should not have any issues with timeouts.


---

## Comment 8

> Username: mborland  
> Created_at: 2021-01-19 15:42:57 UTC  
> Updated_at: 2021-01-19 15:43:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-762927163  

@ckormanyos Test failures will get captured in the shell and look [something like this.](https://github.com/boostorg/math/runs/1723290345?check_suite_focus=true#step:16:582). The last [several lines](https://github.com/boostorg/math/runs/1723290345?check_suite_focus=true#step:16:1079) give you the recap and error if you had any test failures. You can either download the log to grep through it, or you can use the search box provided.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-01-20 15:04:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-763686244  

>  Test failures will get captured in the shell...  
  
Thank you @mborland for your advice and also for the original ground breaking GitHub Actions work in Math. Yes. I am able to identify CI failures properly now.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2021-01-20 19:08:13 UTC  
> Updated_at: 2021-01-20 19:09:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-763868018  

This thing is moving forward.  
  
However, @jzmaddock if you get a chance, there are two test failures on this preliminary CI with MinGW 8.1 64-bit on Win.  
  
At [this place in the log](https://github.com/boostorg/multiprecision/runs/1735614041?check_suite_focus=true#step:11:101), I find that `test_sf_import_c99` for `TEST_CPP_DEC_FLOAT_5` and `TEST_CPP_DEC_FLOAT_6` are failing CI, but only on this compiler.  
  
When I extract the test code in a standalone project on Boost1.75, MinGW 8.2 on my own box, all those tests pass.  
  
Do you have any idea what might be happening in this isolated case? I notice the CI is using 1.76, but I have 1.75. I am also running GCC 8.2 compared with 8.1 on the CI. But I can not reproduce these errors.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2021-01-20 21:18:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-763953086  

It works fine for me with later gcc versions, I was able to reproduce with an old gcc-8 I had lying around and the program runs fine until after main() returns and then segfaults:  
  
```  
Thread 1 received signal SIGSEGV, Segmentation fault.  
0x000000006fc59ce7 in libstdc++-6!_ZN11__gnu_debug30_Safe_unordered_container_base7_M_swapERS0_ () from D:\compilers\mingw\8.1\mingw64\bin\libstdc++-6.dll  
(gdb) bt  
#0  0x000000006fc59ce7 in libstdc++-6!_ZN11__gnu_debug30_Safe_unordered_container_base7_M_swapERS0_ ()  
   from D:\compilers\mingw\8.1\mingw64\bin\libstdc++-6.dll  
#1  0x000000006fc56c14 in libstdc++-6!.gcclibcxx_demangle_callback ()  
   from D:\compilers\mingw\8.1\mingw64\bin\libstdc++-6.dll  
#2  0x000000006fc41171 in ?? ()  
   from D:\compilers\mingw\8.1\mingw64\bin\libstdc++-6.dll  
#3  0x000000006fc41258 in ?? ()  
   from D:\compilers\mingw\8.1\mingw64\bin\libstdc++-6.dll  
#4  0x00007ff822a87cad in ntdll!RtlActivateActivationContextUnsafeFast ()  
   from C:\WINDOWS\SYSTEM32\ntdll.dll  
#5  0x00007ff822ab3ff1 in ntdll!LdrShutdownProcess ()  
   from C:\WINDOWS\SYSTEM32\ntdll.dll  
#6  0x00007ff822ab3e8d in ntdll!RtlExitUserProcess ()  
   from C:\WINDOWS\SYSTEM32\ntdll.dll  
#7  0x00007ff820d7e0ab in KERNEL32!FatalExit ()  
   from C:\WINDOWS\System32\kernel32.dll  
#8  0x00007ff820aea155 in msvcrt!_exit () from C:\WINDOWS\System32\msvcrt.dll  
#9  0x00007ff820aea7c5 in msvcrt!_initterm_e ()  
   from C:\WINDOWS\System32\msvcrt.dll  
#10 0x00000000004014a5 in __tmainCRTStartup ()  
#11 0x00000000004014fb in mainCRTStartup ()  
```

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-01-21 08:44:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764477225  

> It works fine for me with later gcc versions, I was able to reproduce with an old gcc-8 I had lying around and the program runs fine until after main() returns and then segfaults:  
  
Thank you @jzmaddock for confirming this.  
  
This is one of those things that makes you say, hmmmm. I tried to add stack size for that compiler, but no help there.  
  
I can get the older compiler and try to identify the root cause of the seg faults, as this seems to be a compiler-dependent issue. We could install and/or cache a more modern gcc-mingw compiler on GitHub Actions. We could even try to motivate installation of a GCC9 or higher on the platform, as they are stuck on 8.1 gcc-mingw (albeit x64, which is cool), noting that chocolatey has higher versions.  
  
... Exclude those tests from gcc-mingw...?  
  
No real rush on this, ... but ... Ideas?

---

## Comment 13

> Username: mborland  
> Created_at: 2021-01-21 15:55:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764742242  

@ckormanyos The move to actions in math also resulted in issues from older compilers. The solution for several was to use macros to disable specific compilers/platforms, especially when more modern versions of a compiler gave the correct results. It took a good 2-3 weeks to get to green.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2021-01-21 17:17:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764804824  

> The move to actions in math also resulted in issues from older compilers. The solution for several was to use macros to disable specific compilers/platforms, especially when more modern versions of a compiler gave the correct results. It took a good 2-3 weeks to get to green.  
  
Thank you @mborland that suggestion is very sensible and reasonable. I will try some of those kinds of workaround.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2021-01-21 18:00:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764832587  

@ckormanyos I'm fairly sure this is an order-of-destruction issue, but I haven't yet been able to figure out what specific code is causing the issue to verify it's not our fault.  It would be nice to verify that this is indeed a gcc bug (I suspect the std lib has been destructed prior to one or more of our globals).

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2021-01-21 18:36:32 UTC  
> Updated_at: 2021-01-21 18:37:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764853165  

> this is an order-of-destruction issue...  
  
Great idea, John.  
  
That sort of jives with the _feeling_ of this thing. I tried stack increase to no avail. There are not huge amounts of dynamic allocation either, so that again, makes dtors in `atexit()` and its buddies a likely one. I will look in the file for static instances because sometimes even if this problem is present, there can be a workaround  --- even if said workaround has a bit of _empirical_ character.

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2021-01-21 18:41:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764855666  

> this is an order-of-destruction issue...  
  
On GCC there are some super crazy tuning options. I use them a lot on the metal of hard real-time, flags like `-fuse-cxa-atexit` and `-fno-use-cxa-atexit`. Maybe an idea?

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2021-01-21 18:52:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764861829  

OK, I'm 90% sure this is a GCC/Mingw issue, the reduced test case is:  
  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
template <class T>  
void test()  
{  
   static thread_local T results[50] = {};  
}  
  
int main()   
{  
   test<boost::multiprecision::number<boost::multiprecision::cpp_dec_float<59, long long, std::allocator<char> > > >();  
   return 0;  
}  
```  
  
Which segfaults during destruction of `results`.  Remove the `thread_local` specifier and all's fine.

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2021-01-21 18:59:35 UTC  
> Updated_at: 2021-01-21 18:59:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764866326  

> Remove the thread_local specifier and all's fine.  
  
Awesome! What a masterful find, John. Thank you.  
  
Should I spruce up those two cases in the Jamfile with something like `-fno-threadsafe-statics` ?

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2021-01-21 19:14:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764874735  

Seems to be setup dependent: https://stackoverflow.com/questions/32751591/thread-local-vector-segmentation-fault-at-end-of-program-in-c

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2021-01-21 19:17:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764876064  

Haha, apparently I've reported it previously: https://sourceforge.net/p/mingw-w64/bugs/527/

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2021-01-21 20:09:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764906492  

@ckormanyos I think I might need to download a few different mingw versions and see which "flavours" this occurs with before we decide what to do about CI.  Does anyone know if it's possible to detect the mingw threading model (POSIX or WIN32) in the preprocessor?

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2021-01-21 21:26:30 UTC  
> Updated_at: 2021-01-21 21:27:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-764951751  

> Does anyone know if it's possible to detect the mingw threading model  
  
The compiler knows that on the command-line level (graphic below). However, I'm not sure if there are any internal switches set by the thread model. I will look around as well tomorrow.  
  
![grafik](https://user-images.githubusercontent.com/2404721/105414247-85a3e700-5c37-11eb-8036-aa285c482b20.png)

---

## Comment 24

> Username: mborland  
> Created_at: 2021-01-22 11:09:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-765328373  

@jzmaddock The config info for the run has macros for the thread model. [Posix](https://github.com/boostorg/multiprecision/runs/1738138895?check_suite_focus=true#step:10:90) and [Win threads](https://github.com/boostorg/multiprecision/runs/1738139099?check_suite_focus=true#step:10:101) examples.

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2021-01-22 11:54:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-765348371  

OK, I think I have a handle on this:  
  
There are basically 3 mingw flavours:  
  
* Msys, which is maintained and up to date with gcc releases.  
* The sourceforge mingw snapshots which are stalled at gcc-8.1 and come in win32 or posix threading models.  
  
Of these, only the win32 threading model binaries *do not* define _REENTRANT, and have the issue so:  
  
We have 2 issues to solve:  
  
* What to do for end users.  
* What to do about CI.  
  
The original failure comes from boost::math::zeta (called by tgamma/lgamma) being called with a type with a non-trivial destructor, so how about:  
  
* When we use `thread_local` and we know that the platform is the problematic mingw, we add a static_assert that the type has a trivial destructor with a suitable error message.  This keeps things doing the right thing for the common case that the floating point type does have a trivial destructor while signally loud and clear that that particular usage isn't supported on that platform.  
* #if out the problematic calls in the tests when the same situation occurs.  
  
Thoughts?

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2021-01-22 12:24:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-765362563  

> When we use thread_local and we know that the platform is the problematic mingw, we add a static_assert that the type has a trivial destructor with a suitable error message. This keeps things doing the right thing for the common case that the floating point type does have a trivial destructor while signally loud and clear that that particular usage isn't supported on that platform.  
  
There is really _a lot_ of information here. Thanks, John, for sorting this out and paving a pathway to success.  
  
On the technical thoughts, yes, we can now use the official C++11 `static_assert`, so this `#error` you signalize will be tidy and easy to write.  
  
How can we detect the problematic compilers? I have a MinGW GCC 8.2 that does not have this problem. THis was built specifically on MSYS. So I think the problematic stalled out ones are really limited to MINGW ports 8.1 and lower.  
  
Furthermore, I am aware of MinGW GCC ports supporting all three of `WIN32`, and `__GNUC__`, and `__MINGW__`. In the past when I have wanted to identify, in particular GCC on MinGW, I have used stuff like below. Join these thoughts in with the fact that `__GNUC__` and friends have numbers, all of this, I believe, you've used in the past I believe. You might end up with something like this... (I'm not familiar with `_REENTRANT`, but I tried to add it to the logic).  
  
```  
#if defined(__GNUC__) && defined(__MINGW__)  
  
#if (__GNUC__ < 9) && (__GNUC_MINOR__ < 2) && defined(_REENTRANT)  
static_assert(false, "Error: bad behavior");  
#endif  
  
#endif  
```

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2021-01-22 13:14:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-765388225  

> The config info for the run has macros for the thread model. Posix and Win threads examples.  
  
Nearly but not quite: one of those is VC++, and we're talking about 2 different GCC mingw "flavours" :(

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2021-01-22 20:05:56 UTC  
> Updated_at: 2021-01-22 20:06:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-765654507  

So after a few tries, numerous builds and tests seem to be running successfully on GitHub Actions, some GCC Ubuntu, others MinGW on Win-host and VC14.0, 14.2 on Win-host. There are no MAC-OS or clang builds at the moment in the YAML of this PR. This PR also does not exercise any of the multiprecision special functions tests.  
  
This PR includes a _weak_ (not strong) workaround for MinGW GCC 8.1 on the CI machine(s). It avoids the non-trivial TLS destructors altogether via preprocessor definitions placed directly in `test_sf_import_c99.cpp`. A stonger workaround is in [MinGW thread local in math](https://github.com/boostorg/math/pull/497)  
  
This branch has successively merged the changes from #288. So these branches should be roughly synchronized at the moment.  
  
The next step includes adding MAC-OS builds and adding other compiler and language standards in order to round out the CI on GitHub Actions.  
  
@jzmaddock this is going pretty well now, so if you get a chance, could you please comment on the scope of tests included in GitHub Actions at the moment in this PR and suggest ways to round these out?

---

## Comment 29

> Username: jzmaddock  
> Created_at: 2021-01-23 11:06:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-765906238  

Hi Chris, thanks for these, this is indeed going rather well!  
  
I think we may need to get a bit systematic about this and make a list of all the permutations we want to test and then decide how to divide them up between the different CI's.  Also probably retire the Travis and appveyor tests?  
  
For Windows: I'm not sure that we're testing msvc in all the different language configurations - C++11 for msvc-14.0 and 14,17 and 20 for 14.2.  This may not sit well with testing mingw in the same GHA matrix, but we can offload some to drone.  
For Linux, we need to update to gcc/clang-10 and get some C++20 tests in there - I've done that for drone on Boost.Config if it's not so easy on GHA.  
For MacOS, again we need to update to apples latest tools, and again, that's do-able on drone if it's easier to leave the Mac stuff on there.  
  
I also worry, but have no solution for, the fact that our tests are very Intel-x64 centric.  Also very focused on msvc/gcc/clang at the expense of Intel/IBM/Oracle/Cray etc compilers.  There's no easy solution to this unfortunately, the guys running drone appear to be open to integrating with other platforms if there's someone to run them, but that's still a big if.  
  
With regard to the sibling C++11-isation PR to this one, I think I'm about done.  There's a few things I need to check and possible take out again (not sure if the C++17 stuff helps as much as I thought it would).  Have you spotted anything I've missed or that could usefully be modernised?

---

## Comment 30

> Username: ckormanyos  
> Created_at: 2021-01-23 16:17:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-766112261  

I will deal with the full depth of @jzmaddock's excellent comments shortly.  
  
Right off the bat,  
>  Have you spotted anything I've missed or that could usefully be modernised?  
  
I left a basic set of potential review comments in #288.

---

## Comment 31

> Username: ckormanyos  
> Created_at: 2021-01-23 16:25:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-766122309  

Back to @jzmaddock's excellent comments...  
  
> I think we may need to get a bit systematic about this and make a list of all the permutations we want to test and then decide how to divide them up between the different CI's. Also probably retire the Travis and appveyor tests?  
  
Agreed. There are several philosophies. One tries to make GHA have the same depth and scope as drone. Then they substitute for each other. Another philosophy, that I might even favor, would be to reduce the set of GHA to select compilers, language flavors and OS-es in order to give an quick indication that things are OK. If you cracker a push, you'll soon see that on GHA and you could cancel drone and save the cycles until you're close enough... Anyway, we should discuss this with the colleagues.  
  
> For Windows: I'm not sure that we're testing msvc in all the different language configurations - C++11 for msvc-14.0 and 14,17 and 20 for 14.2.  
  
Agreed on all. I would actually separate the MinGW and MSVC jobs from each other in uniquely identifiable groups.  
  
> I also worry, but have no solution for, the fact that our tests are very Intel-x64 centric. Also very focused on msvc/gcc/clang at the expense of Intel/IBM/Oracle/Cray etc compilers.  
  
In my domain, I usually see the need to _go down_ to stuff like ARM(R)-8 and below. It's easier for me to get on a BeagleBone. Jumping up to a Cray is difficult, I's totally need help with that one. How many clients need to go down or up, I do not know? 1%, 10%, it's still a lot. So any coverage we can improve here would be good. I am willing to work on this.  
  
> With regard to the sibling C++11-isation PR to this one, I think I'm about done.  
  
I left a bunch of optional comments in #288. But I fear they might not be too nit-picky or might be outdated... Please disregard if so.

---

## Comment 32

> Username: jzmaddock  
> Created_at: 2021-01-23 17:24:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-766144939  

Just a quick note to say that I raised the lack on non-x86 tests on the ML, and discovered that Travis is actually moving to support other architectures: https://docs.travis-ci.com/user/multi-cpu-architectures/  So possibly the future of travis might be the non-x86 stuff.

---

## Comment 33

> Username: ckormanyos  
> Created_at: 2021-01-24 09:11:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-766315619  

Some tests are failing on `std=gnu++20` with GCC-10 in drone. This is motivation to potentially add more compiler/OS/standards combinations to the matrix in GHA.

---

## Comment 34

> Username: ckormanyos  
> Created_at: 2021-01-24 09:14:28 UTC  
> Updated_at: 2021-01-24 09:16:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-766315956  

> ... the fact that our tests are very Intel-x64 centric.  
  
Started working on simulated ARM CI runs within the context of [another domain](https://github.com/ckormanyos/real-time-cpp/issues/96). If (or when) this activity is in a clean, finished state, could potentially be interesting for running selected Boost tests on GHA in simulated ARM targets on QEMU or similar simulator.

---

## Comment 35

> Username: jzmaddock  
> Created_at: 2021-01-24 09:53:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-766320495  

>Some tests are failing on std=gnu++20 with GCC-10 in drone. This is motivation to potentially add more compiler/OS/standards combinations to the matrix in GHA.  
  
Nod.  C++20 adds some operator rewriting rules that breaks existing code.  In this case it appears to be breaking Eigen rather than us, so I've disabled Eigen-interop testing for C++20 for now.  
  
>Started working on simulated ARM CI runs within the context of another domain. If (or when) this activity is in a clean, finished state, could potentially be interesting for running selected Boost tests on GHA in simulated ARM targets on QEMU or similar simulator.  
  
Thanks!  I think we need to try Travis's native support for other architectures, Azure Pipelines are supposed to have native support too, but I can't find any docs :(  Hopefully GHA will catch up at some point!

---

## Comment 36

> Username: ckormanyos  
> Created_at: 2021-01-25 19:27:36 UTC  
> Updated_at: 2021-01-25 19:28:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-767056451  

Hi @jzmaddock: We significantly increased the depth of language standards and compilers in this run.  
  
- The awesome `typedef` to `using` changes have been pulled.  
- Added more modern GCC/clang compilers and language standards on ubuntu-focal  
- Added more modern language standards on windows-latest  
- Added semi-old GCC/clang on ubuntu-bionc.  
  
The run was very good with three tests failing, all on clang-7, ubuntu-bionic, test-suite `misc`. It looks like potential problems with rational conversion, maybe an issue with random number generation and what, to my untrained eye, might be confusion with built-in/multiple-precision conversion.  
  
I can look more deeply into these few failures tomorrow.  
  
After that, I would like to add the remaining missing runners. These include all MacOS runners and also very-old GCC/clang on ubuntu-xenial.  
  
Comments?  
  
![grafik](https://user-images.githubusercontent.com/2404721/105755480-a035d300-5f4b-11eb-8745-064c013b42b0.png)

---

## Comment 37

> Username: jzmaddock  
> Created_at: 2021-01-26 15:48:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-767634480  

The failures are all clang-7 related, and fail with -O3 but not -O0.

---

## Comment 38

> Username: jzmaddock  
> Created_at: 2021-01-26 16:47:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-767675015  

I did a bit of printf-debugging and added:  
  
```  
         std::cout << std::hexfloat << f << std::endl << term << std::endl;  
         std::cout << static_cast<limb_type>(term) << std::endl;  
```  
  
to cpp_int.hpp:1582  
  
and with test program:  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
  
  
int main()  
{  
    double test_val = 8.358295116950815370e+62;  
    boost::multiprecision::cpp_rational rat(test_val);  
    std::cout << rat << std::endl;  
    std::cout << (double)numerator(rat) << std::endl;  
    std::cout << (double)denominator(rat) << std::endl;  
    double result = (double)rat;  
    std::cout << result << std::endl;  
    if(result == test_val)  
      std::cout << "yeh!!" << std::endl;  
    else  
    {  
        std::cout << "Ooops!!" << std::endl;  
    }  
}  
```  
  
see:  
  
```  
0x8.208d4b2dc0d3p+60  
0x8.208d4b2dc0d3p+60  
9369972889405435904  
835829511695081537047622450819715645709872864824951181469024256  
0x1.0411a965b81a6p+209  
0x1p+0  
0x1.0411a965b81a6p+209  
yeh!!  
```  
  
In debug mode (correct), but as soon as optimisations are turned on I get:  
  
```  
0x8.208d4b2dc0d3p+60  
0x8.208d4b2dc0d3p+60  
9369972877123649536  
835829510599509583545403007098680768645468250418206286653947904  
0x1.0411a96p+209  
0x1p+0  
0x1.0411a96p+209  
Ooops!!  
```  
  
Notice how the values for "term" agree (0x8.208d4b2dc0d3p+60), but for `static_cast<limb_type>(term)` do not!  As a result the numerator in the rational is all wrong.  
  
I've been unable to reduce the test case anymore, do you have any ideas?

---

## Comment 39

> Username: ckormanyos  
> Created_at: 2021-01-26 21:44:26 UTC  
> Updated_at: 2021-01-26 21:47:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-767847366  

> I've been unable to reduce the test case anymore, do you have any ideas?  
  
Cool, a localized test case. I will get the compiler and take a deeper look.  
  
Crazy ideas, ... precise-FP? fast math, or the like --- something that might be automatically activated when optimization gets activated. Then again, there were also sometines inconsistencies when double was 80-bit on some of those older compilers. But I think it's using x64?  
  
This afternoon, I had not seen your new investigations, so kind of running on intertial, I went ahead and disabled the offending tests. So they are localized.  
  
Along that path, I was thinking with these tests deactivated, I could add more even older compilers and MacOS runners, thus giving us an idea if there are any more problems to encounter and, if so, how many. Is that OK for you? Or should we deal with this issue at hand first. I kind of deactivated them without (or before) seeing your recent post...

---

## Comment 40

> Username: ckormanyos  
> Created_at: 2021-01-26 21:49:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-767849744  

Somehow the digits differ at the precision range of single-precision float. Coincidence?

---

## Comment 41

> Username: ckormanyos  
> Created_at: 2021-01-27 06:48:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-768073422  

>> I've been unable to reduce the test case anymore, do you have any ideas?  
  
> Cool, a localized test case. I will get the compiler and take a deeper look.  
  
_Smells_ like FP-precision or integer promotion variances.  
  
Since the code runs without exception or core dump, as soon as I get a chance, I can attempt to find this with brute force --- put in lots of prints, potentially using these with kind of a binary search through the code lines. Sometimes the sheer act of putting in prints causes the compiler to re-think it and get the right or a different answer. That also tells a story.

---

## Comment 42

> Username: ckormanyos  
> Created_at: 2021-01-27 19:37:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-768527697  

> as soon as I get a chance, I can attempt to find this with brute force  
  
Some unforeseen business in other areas has arisen and I will not be able to work on this for a few days --- at the soonest. Will pick it up where left off after that time.

---

## Comment 43

> Username: jzmaddock  
> Created_at: 2021-01-28 18:00:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769268095  

No problem Chris: clang-7 issue should now be worked around in the cxx11 branch/pr.  See: https://github.com/boostorg/multiprecision/pull/288/commits/3329a9654480f1660a61cb8a77d259d92d970caa

---

## Comment 44

> Username: ckormanyos  
> Created_at: 2021-01-28 19:09:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769308643  

> clang-7 issue should now be worked around in the cxx11 branch/pr.  
  
Great! Thank you, John.  
  
Whew... Great result and workaround. I was just about to report that I could not reproduce the error with several clang++7 setups. I tries clang++7.0.0 via C++ includes from VC14.0 and could not find errors.  
  
![clang_session](https://user-images.githubusercontent.com/2404721/106186562-bf7b6d00-61a4-11eb-8ffd-748d0d2b5590.JPG)

---

## Comment 45

> Username: ckormanyos  
> Created_at: 2021-01-28 19:10:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769309077  

> Some unforeseen business in other areas has arisen and I will not be able to work on this for a few days  
  
Those conflict have been resolved and I can work again on this.

---

## Comment 46

> Username: jzmaddock  
> Created_at: 2021-01-28 19:23:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769316562  

Nod msvc clang is a rather different beast... there are still some outstanding failures for clang-11 (and earlier) on msys64 which look similar but different to the Ubuntu ones.  
  
Meanwhile.... more things to test, I've just pushed a massive change to the drone config to the cxx11 PR that carpet bomb tests lots of permutations.  It occurs to me that we could test even numbered gcc/clang versions on GHA and odd numbered ones on drone to spread the load a bit?  
  
We also need to mix -std=gnu++NN and -std=c++NN a bit.  
  
And get some sanitizers in there, probably with just the latest clang release or something?  We might be cruising-for-a-bruising with that (false positives), but it would be good to try.

---

## Comment 47

> Username: ckormanyos  
> Created_at: 2021-01-29 06:55:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769617456  

> Meanwhile.... more things to test, I've just pushed a massive change to the drone config to the cxx11 PR that carpet bomb tests lots of permutations. It occurs to me that we could test even numbered gcc/clang versions on GHA and odd numbered ones on drone to spread the load a bit?  
  
Seems like a good plan to me. Nice.  
  
> We also need to mix -std=gnu++NN and -std=c++NN a bit.  
  
Do you mean redundantly with each doing c++/gnu++ or checkerboard with some having c++/others having gnu++ ?

---

## Comment 48

> Username: jzmaddock  
> Created_at: 2021-01-29 09:51:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769701431  

>Do you mean redundantly with each doing c++/gnu++ or checkerboard with some having c++/others having gnu++ ?  
  
Not sure, normally I run with `-std=gnu++NN` as that tests more stuff, but maybe one runner with `-std=c++NN -pedantic` might be a good torture test?

---

## Comment 49

> Username: ckormanyos  
> Created_at: 2021-01-29 18:50:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769983762  

>  but maybe one runner with -std=c++NN -pedantic might be a good torture test?  
  
Yep. That seems like wise advice. When I strive for extremely strict warning reduction, say for C++11, in the GCC-world, I use:  
  
```  
g++ -std=c++11 -Wall -Wextra -pedantic  
```  
  
One final flag `-Werror` could be considered, it means we actually treat warnings as errors. This state can be difficult to achieve, especially if some other Boost modules might be the source of the warnings. So only go for this if the compileris relatively modern, but not too modern, uses a well-established C++ standard, and the code is relatively stable, dependency-low and not expected to be in dynamic change so much.

---

## Comment 50

> Username: jzmaddock  
> Created_at: 2021-01-29 18:50:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769983777  

@ckormanyos I've pushed some changes to get things clean with -fsanitize=address and I have one unresolved issue with leaks in exercise_threading_log_agm.cpp with BOOST_MULTIPRECISION_EXERCISE_THREADING_BACKEND_TYPE=104.  Both gcc and clang show this, but neither are giving me a stack trace for where these are coming from.  Any ideas?

---

## Comment 51

> Username: ckormanyos  
> Created_at: 2021-01-29 18:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-769984718  

> but neither are giving me a stack trace for where these are coming from. Any ideas?  
  
Thanks John. Great find. That is actually a cool example, so I do indeed want to increase its robustness.  
  
I will take a look. The weakest part of the code might actually be the thread pool.

---

## Comment 52

> Username: ckormanyos  
> Created_at: 2021-01-30 09:12:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-770181909  

> I will take a look.   
  
[This push](https://github.com/boostorg/multiprecision/pull/291/commits/38b4909972e0819040caea520dd3d2684bc46c51) I did sanitize what I could mentally. My compilers do not reach this area of non-sanitized. There were, in fact, some weaknesses on shared data on the talbe of primes being used and a synchronization of the Boolean result of the test.  
  
If this does not resolve the sanitization issues, I was thinking the contants pi or log(2), as these originate deep within MPFR might lead to some thread-synchronization problems and potentially memory sanity check issues. But that's a stretch of the mind.

---

## Comment 53

> Username: jzmaddock  
> Created_at: 2021-01-30 10:23:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-770189571  

> If this does not resolve the sanitization issues, I was thinking the contants pi or log(2), as these originate deep within MPFR might lead to some thread-synchronization problems and potentially memory sanity check issues. But that's a stretch of the mind.  
  
Good catch: we do that anyway by having a static object call mpfr_free_cache at program exit, but that doesn't clear thread-local caches, I'll see what I can conjure up (which is to say this might be our bug after all).

---

## Comment 54

> Username: ckormanyos  
> Created_at: 2021-01-30 12:48:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-770207533  

> I'll see what I can conjure up (which is to say this might be our bug after all)  
  
I will also poke around...  
  
[This push](https://github.com/boostorg/multiprecision/pull/291/commits/623a7da43129d61329889ec79d2f2cc9b51927d5) replaces Boost math constants with local string-constructed static copies. This is not done to be necessarily a better design or improve the example. Rather, it allows to reduce the dependencies on this file and potentially make it easier to track down potential sanitation issues?

---

## Comment 55

> Username: jzmaddock  
> Created_at: 2021-01-30 16:11:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/291#issuecomment-770235033  

MPFR thread cleanup fixed in https://github.com/boostorg/multiprecision/pull/288/commits/5116bf1fa2ab2c7c639766c481b7bbed5898795e.

---
