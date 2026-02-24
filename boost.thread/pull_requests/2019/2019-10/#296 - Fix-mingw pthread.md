# #296 Fix/mingw pthread [Open]

> Username: theodelrieu  
> Created at: 2019-10-16 10:39:33 UTC  
> Updated at: 2019-10-31 15:21:31 UTC  
> Url: https://github.com/boostorg/thread/pull/296  

Hello,  
  
I've been trying to use Boost.Thread while cross-building from Linux to Windows, using mingw-w64, and stumbled upon the issue mentioned in #157. I tried to solve it without breaking compatibility.  
  
Also, `getpagesize` and `sysconf` are not available when using MinGW, so I used [GetSystemInfo](https://docs.microsoft.com/en-us/windows/win32/api/sysinfoapi/nf-sysinfoapi-getsysteminfo) to retrieve the page size.

---

## Comment 1

> Username: theodelrieu  
> Created_at: 2019-10-17 09:27:20 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-543089316  

@viboes Do you know what causes "The system cannot find the batch label specified - Config_VC12"?  
  
Apparently it is a batch file issue, so I guess it is not related to this PR.

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-10-21 16:18:11 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-544591576  

The original code defaults to `pthread` (when the toolset is neither `msvc` nor `gcc`), whereas your replacement does not. No idea whether this is a problem or not.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-10-21 16:22:07 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-544593239  

The `Config_VC12` error is caused by an incompatibility between `b2` and Appveyor's 2013 image. The fix is to use the 2015 image. You can add this change to `appveyor.yml` to your PR (change `APPVEYOR_BUILD_WORKER_IMAGE: Visual Studio 2013` to `APPVEYOR_BUILD_WORKER_IMAGE: Visual Studio 2015`.)

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-10-21 16:29:38 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-544596698  

The 2017 failure is caused by a path being too long; the option `--abbreviate-paths` was once used, and has been inadvertently dropped by https://github.com/boostorg/thread/commit/f1d464d0d5345c8c1eafd848e210f8b08cff885f#diff-180360612c6b8c4ed830919bbb4dd459. It probably needs to be reinstated.

---

## Comment 5

> Username: theodelrieu  
> Created_at: 2019-10-22 08:29:47 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-544858184  

@pdimov From the rule's name (`win32_pthread_paths`), I assumed this code path was only taken when using pthreads on win32. Furthermore, I don't see how the following line could work if a toolset different from `msvc` and `gcc` gets used, assuming `libname` is `pthread`:  
  
`lib_path = [ path.glob $(lib_path) : $(libname) ] ;`

---

## Comment 6

> Username: theodelrieu  
> Created_at: 2019-10-23 09:08:35 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-545347313  

The failure reported by Travis seems to be related to visibility:  
  
>libs/thread/test/sync/conditions/condition_variable_any/wait_until_pass.cpp(77): test '(d).count() < (ns(max_diff)).count()' ('75000000' < '75000000') failed in function 'void f()'  
>  
>1 error detected.  
  
>...failed testing.capture-output bin.v2/libs/thread/test/condition_variable_any__wait_until_p_lib.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable_any__wait_until_p_lib.run...  
  
The Appveyor fails are multiple:  
  
* The Cygwin/MinGW jobs result in a GCC ICE  
* The only MSVC that fails complains about memory leaks  
  
@pdimov I will need your help once again! Needless to say I have no idea what is going on 😅

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-10-29 12:17:36 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-547392254  

I see nothing visibility-related in the Travis failure.

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-10-29 12:21:18 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-547393445  

The msvc-14.0 failure is this:  
  
```  
c:/projects/boost-root/libs/thread/test/./util.inl(171): fatal error: in "test_creation_through_reference_wrapper": Timed test didn't complete in time, possible deadlock.  
```  
  
The Cygwin/mingw jobs are timing out at 1h30m.  
  
These things need a look-over by the maintainer. None of the failures seem caused by your changes, but until develop is green on Travis and Appveyor, it will be hard to tell.

---

## Comment 9

> Username: theodelrieu  
> Created_at: 2019-10-31 15:21:31 UTC  
> Url: https://github.com/boostorg/thread/pull/296#issuecomment-548427545  

I've added a patch to not use monotonic clocks with MinGW + pthread, and to not trigger an assertion when calling `pthread_attr_setclock`.  
  
From what I understand from the [implementation]( https://github.com/Alexpux/mingw-w64/blob/d0d7f784833bbb0b2d279310ddc6afb52fe47a46/mingw-w64-libraries/winpthreads/src/cond.c#L139), they are not supported.  
  
I didn't want to fiddle to much with the preprocessor directives, so right now the user has to define `BOOST_THREAD_POSIX` when compiling, otherwise a `#error` triggers.  
  
If you have a better way I'm all ears!

---
