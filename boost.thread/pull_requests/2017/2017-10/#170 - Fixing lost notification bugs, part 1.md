# #170 Fixing lost notification bugs, part 1 [Closed]

> Username: austin-beer  
> Created at: 2017-10-05 05:15:47 UTC  
> Updated at: 2019-03-31 03:03:32 UTC  
> Closed at: 2017-10-11 16:08:39 UTC  
> Url: https://github.com/boostorg/thread/pull/170  

- Updated all of the functions in all of the condition variable classes to ensure they don't lose notifications.  
- Fixed an issue where the no_interruption_point::sleep_*() functions were still interruptible on Windows.  
- Deleted v2/thread.hpp and added platform-specific versions of the sleep_*() functions to pthread/thread_data.hpp and win32/thread_data.hpp, which was necessary in order to fix the previously-mentioned bug.  
- Added no_interruption_point::sleep() functions to pthreads to be consistent with Windows.  
  
I apologize for the size of this PR, but most of the changes are copy/paste changes.  
  
In part 2 I'll review and update the mutex and future classes.  
  
Updated test results are available at: https://github.com/austin-beer/test-time-jumps

---

## Comment 1

> Username: austin-beer  
> Created_at: 2017-10-05 20:31:42 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334583396  

Only one Travis CI error failed. I think this may just be a transient timing error and not indicative of a real error, since it didn't occur on any of the other builds.  
  
```  
libs/thread/test/sync/mutual_exclusion/recursive_timed_mutex/try_lock_for_pass.cpp(54): test 'm.try_lock_for(ms(250)) == false' failed in function 'void f2()'  
```  
  
On Windows with Visual Studio 2008 and 2010, AppVeyor is failing with the following error.  
  
```  
[00:03:26] compile-c-c++ bin.v2\libs\thread\test\condition_variable_any__wait_until_pred_p.test\msvc-10.0\debug\threadapi-win32\threading-multi\sync\conditions\condition_variable_any\wait_until_pred_pass.obj  
[00:03:26] wait_until_pred_pass.cpp  
[00:03:26] C:\projects\boost-root\boost/thread/win32/thread_primitives.hpp(97) : error C2039: 'GetTickCount' : is not a member of 'boost::detail::winapi'  
[00:03:26] C:\projects\boost-root\boost/thread/win32/thread_primitives.hpp(97) : error C3861: 'GetTickCount': identifier not found  
```  
  
I'm not sure why this is happening because the same test compiles for me when I build with Visual Studio 2010 locally. I'm open to suggestions...

---

## Comment 2

> Username: austin-beer  
> Created_at: 2017-10-05 21:15:19 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334594353  

Ah, I think I've figured it out. AppVeyor is using the wrong version of Visual Studio. See the following PR:  
  
https://github.com/boostorg/thread/pull/171

---

## Comment 3

> Username: austin-beer  
> Created_at: 2017-10-05 21:41:37 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334600165  

Ignore my previous comment. I just didn't understand AppVeyor very well. I'm still not sure why the build is failing.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-10-05 23:40:26 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334621178  

Hi, thanks to try to improve the PR.  
  
For Windows I don't know. I'll try to do a minimal change to check if in my branch we have the same issue now.  
  
The issue in travis could be as well an issue on the test program that we need to track. I w'll re-run the test.  
  
I don't see any specific test that show that we don't loss notifications. I 'expected that you would add them before. Or am I misinterpreting the results?

---

## Comment 5

> Username: austin-beer  
> Created_at: 2017-10-05 23:46:45 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334622067  

Ok, thanks. I'm wondering if the AppVeyor environment for VS 2008, 2010, and 2012 isn't being set up exactly correctly. The following lines appear in all three logs, and the `12.0` is suspicious to me.  
  
```  
[00:03:20] b2 headers  
[00:03:21] ...found 1 target...  
[00:03:21] ...updating 1 target...  
[00:03:21] msvc.write-setup-script C:\Users\appveyor\AppData\Local\Temp\1\b2_msvc_12.0_vcvarsall_x86.cmd  
[00:03:21] ...updated 1 target...  
[00:03:21] ...found 1 target...  
[00:03:21] ...updating 1 target...  
[00:03:21] msvc.write-setup-script C:\Users\appveyor\AppData\Local\Temp\1\b2_msvc_12.0_vcvarsall_amd64.cmd  
[00:03:21] ...updated 1 target...  
[00:03:21] ...found 1 target...  
[00:03:21] ...updating 1 target...  
[00:03:21] msvc.write-setup-script C:\Users\appveyor\AppData\Local\Temp\1\b2_msvc_12.0_vcvarsall_x86_arm.cmd  
```  
  
I haven't yet added any tests to ensure that we don't lose notifications. I still have that on my TODO list. If you'd like me to add those before you merge this PR, let me know. It'll probably take me a couple of days.

---

## Comment 6

> Username: viboes  
> Created_at: 2017-10-06 02:24:23 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334641933  

I've got the same failure on Windows.  
I've contacted Andrey Semashev to see if he know what could be the root cause.

---

## Comment 7

> Username: viboes  
> Created_at: 2017-10-06 02:27:51 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334642336  

It will take some time to me to inspect the PR.  
Maybe this will give you the time to add the tests.  
I believe it will be better if the tests are added on a different branch, so that we can see how my branch behaves respect to these test and how it is improved with yours. What do you think?

---

## Comment 8

> Username: austin-beer  
> Created_at: 2017-10-06 02:30:15 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334642590  

Yes, a separate branch sounds like a good idea. And I assume that you wanted the lost notification tests to be part of the library's automated test cases and not part of my time jump test program, correct?

---

## Comment 9

> Username: viboes  
> Created_at: 2017-10-06 05:58:20 UTC  
> Updated_at: 2017-10-06 05:58:43 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334665614  

Oh, yes this will be even better. As far as we don't need to change the system time, any additional test is more than welcome.  
  
You can add an additional file if you prefer.

---

## Comment 10

> Username: viboes  
> Created_at: 2017-10-07 08:15:12 UTC  
> Url: https://github.com/boostorg/thread/pull/170#issuecomment-334918798  

Austin, for the Windows issue it was introduced by https://github.com/boostorg/predef/pull/57.  
They are working on it.  
  
See also https://github.com/boostorg/predef/issues/54

---
