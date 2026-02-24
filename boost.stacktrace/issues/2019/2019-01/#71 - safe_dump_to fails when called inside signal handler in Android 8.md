# #71 - safe_dump_to fails when called inside signal handler in Android 8 [Closed]

> Username: Apo-  
> Created at: 2019-01-11 11:22:37 UTC  
> Updated at: 2020-12-15 14:00:45 UTC  
> Closed at: 2020-12-15 14:00:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/71  

Boost versions: 1.65.0, 1.69.0  
Platform: Android 8  
  
When calling `safe_dump_to` inside a signal handler in Android 8, no file is created. In fact it seems like we don't successfully exit from `_Unwind_Backtrace` in `boost::stacktrace::detail::this_thread_frames::collect`. Calling the function outside of compromised context creates the file as expected. Also it succeeds in any context on Android 7.  
  
So far I get consistent failures in two different devices (android 8.0, 8.1) and consistent successes in an Android 7 device. I will try to get same phone models in both versions and test it out.  
  
Also I tested to see if I could write a big (100mb) file from the signal handler (something that takes a considerable amount of time) and this completes successfully in all devices.  
  
Minimum Example: https://gist.github.com/Apo-/14c24c27baa86d95fa305e0252ede41f

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-01-11 19:04:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/71#issuecomment-453623019  

This could be a platform limitation. Could you please check the other signals, for example SIGUSR1 and SIGTERM? I hope that the limitations are only for SIGSEGV.

---

## Comment 2

> Username: Apo-  
> Created at: 2019-01-11 19:38:34 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/71#issuecomment-453633479  

I'm quite confident that I tried with SIGABRT as well, but I will check  with the rest of the signals and post the results.   
  
Forgot to mention that `boost::stacktrace::detail::unwind_callback` is called a few times returning `::_URC_NO_REASON`. Sorry by the way for the limited amount of debug information in the report, this has been a week/month from hell :) .

---

## Comment 3

> Username: Apo-  
> Created at: 2019-01-14 10:51:11 UTC  
> Updated at: 2019-01-14 11:23:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/71#issuecomment-453964910  

`sigaction` fails with "Invalid Argument" `errno` on android for `SIGUSR1` so I couldn't test for it, but `SIGTERM` and `SIGUSR2` behave the same way as `SIGSEGV`

---

## Comment 4

> Username: Apo-  
> Created at: 2019-02-13 17:28:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/71#issuecomment-463290304  

It seems like if you don't raise a signal, but a signal is raised (e.g. by dereferencing a null pointer) then it succeeds. I will try to do run more tests and debug this as soon as I get some time.

---

## Comment 5

> Username: apolukhin  
> Created at: 2019-05-04 18:26:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/71#issuecomment-489352397  

Any success?

---

## Comment 6

> Username: Apo-  
> Created at: 2019-05-05 03:03:41 UTC  
> Updated at: 2019-05-05 08:39:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/71#issuecomment-489384327  

Sorry, I haven't made much headway. As I hinted in the last post the problem seems to happen only when the signal is raised by a call to `raise`. Android seems to be implementing it's own `raise` function that calls its own `tgkill`, which I'm assuming it's the culprit, but it's just speculation.  
  
Even when I raise a non-fatal signal (like SIGUSR2) I exit with segmentation fault (this happens with the other signals as well).  
  
```asm  
(lldb) disassemble --pc  
libstacktrace-test.so`boost::stacktrace::detail::this_thread_frames::collect:  
->  0xdbfcfc40 <+88>: str    r0, [sp, #0xc]  
    0xdbfcfc42 <+90>: b      0xdbfcfc44                ; <+92> at collect_unwind.ipp:60  
    0xdbfcfc44 <+92>: ldr    r0, [sp, #0x34]  
    0xdbfcfc46 <+94>: ldr    r1, [sp, #0x28]  
(lldb) bt  
* thread #1, name = 'xample.stacktrace-test', stop reason = signal SIGSEGV: invalid address (fault address: 0x10c)  
  * frame #0: 0xdbfd9176 libstacktrace-test.so`::_Unwind_VRS_Pop(context=0xea0985d0, regclass=<unavailable>, discriminator=16512, representation=<unavailable>) at Unwind-EHABI.cpp:907  
    frame #1: 0xdbfd8e00 libstacktrace-test.so`::_Unwind_VRS_Interpret(context=0xea0985d0, data=0xdbfda620, offset=3, len=4) at Unwind-EHABI.cpp:275  
    frame #2: 0xdbfd9da6 libstacktrace-test.so`libunwind::UnwindCursor<libunwind::LocalAddressSpace, libunwind::Registers_arm>::stepWithEHABI(this=0xea0985d0) at UnwindCursor.hpp:460  
    frame #3: 0xdbfd9a24 libstacktrace-test.so`libunwind::UnwindCursor<libunwind::LocalAddressSpace, libunwind::Registers_arm>::step(this=0xea0985d0) at UnwindCursor.hpp:1339  
    frame #4: 0xdbfd929c libstacktrace-test.so`(anonymous namespace)::unwindOneFrame(state=0, ucbp=0xea098178, context=<unavailable>) at Unwind-EHABI.cpp:184  
    frame #5: 0xdbfd96d4 libstacktrace-test.so`_Unwind_Backtrace(callback=(libstacktrace-test.so`boost::stacktrace::detail::unwind_callback(_Unwind_Context*, void*) + 1 at collect_unwind.ipp:32), ref=0xea098a88) at UnwindLevel1-gcc-ext.c:153  
    frame #6: 0xdbfcfc40 libstacktrace-test.so`boost::stacktrace::detail::this_thread_frames::collect(out_frames=0xea098ad0, max_frames_count=128, skip=1) at collect_unwind.ipp:59  
```  
```  
r0 = 0x0000010c  
r1 = 0x00000110    
r2 = 0x00000007  
r3 = 0x00000000  
r4 = 0xea0980ac  
r5 = 0x00000000    
r6 = 0x00000007  
r7 = 0xea0980d0  
r8 = 0x00004080  
r9 = 0xe7d1e1b8    
sl = 0xea0985d0  
fp = 0x00000001  
ip = 0x00000001  
sp = 0xea098098    
lr = 0xdbff1887  
pc = 0xdbff1176  
cpsr = 0x000e0030  
```  
  
And the backtrace (from android logcat piped to stack) looked like this  
```log  
********** Crash dump: **********  
Build fingerprint: 'google/bullhead/bullhead:8.1.0/OPM7.181205.001/5080180:user/release-keys'  
#00 0x00010176 /data/app/.../lib/arm/libstacktrace-test.so  
_Unwind_VRS_Pop  
/buildbot/src/android/ndk-release-r19/external/libcxx/../../external/libunwind_llvm/src\Unwind-EHABI.cpp:907:26  
#01 0x0000fdfd /data/app/.../lib/arm/libstacktrace-test.so  
_Unwind_VRS_Interpret  
/buildbot/src/android/ndk-release-r19/external/libcxx/../../external/libunwind_llvm/src\Unwind-EHABI.cpp:275:11  
#02 0x00010da3 /data/app/.../lib/arm/libstacktrace-test.so  
libunwind::UnwindCursor<libunwind::LocalAddressSpace, libunwind::Registers_arm>::stepWithEHABI()  
/buildbot/src/android/ndk-release-r19/external/libcxx/../../external/libunwind_llvm/src\UnwindCursor.hpp:460:9  
#03 0x00010a21 /data/app/.../lib/arm/libstacktrace-test.so  
libunwind::UnwindCursor<libunwind::LocalAddressSpace, libunwind::Registers_arm>::step()  
/buildbot/src/android/ndk-release-r19/external/libcxx/../../external/libunwind_llvm/src\UnwindCursor.hpp:1339:18  
#04 0x00010299 /data/app/.../lib/arm/libstacktrace-test.so  
(anonymous namespace)::unwindOneFrame(unsigned int, _Unwind_Control_Block*, _Unwind_Context*)  
/buildbot/src/android/ndk-release-r19/external/libcxx/../../external/libunwind_llvm/src\Unwind-EHABI.cpp:184:7  
#05 0x000106d3 /data/app/.../lib/arm/libstacktrace-test.so  
_Unwind_Backtrace  
/buildbot/src/android/ndk-release-r19/external/libcxx/../../external/libunwind_llvm/src\UnwindLevel1-gcc-ext.c:153:9  
#06 0x00006c3d /data/app/.../lib/arm/libstacktrace-test.so (boost::stacktrace::detail::this_thread_frames::collect(void const**, unsigned int, unsigned int)+84)  
                                                                                                boost::stacktrace::detail::this_thread_frames::collect(void const**, unsigned int, unsigned int)  
                                                                                                /path/to/boost_1_69_0\boost/stacktrace/detail\collect_unwind.ipp:59:5  
#07 0x00006ba5 /data/app/.../lib/arm/libstacktrace-test.so (_ZN5boost10stacktrace6detail18this_thread_frames17safe_dump_to_implIPKcEEjT_jj+64)  
                                                                                                unsigned int boost::stacktrace::detail::this_thread_frames::safe_dump_to_impl<char const*>(char const*, unsigned int, unsigned int)  
                                                                                                /path/to/boost_1_69_0\boost/stacktrace\safe_dump_to.hpp:72:42  
#08 0x00006afd /data/app/.../lib/arm/libstacktrace-test.so (signalHandler+40)  
                                                                                                boost::stacktrace::safe_dump_to(char const*)  
                                                                                                /path/to/boost_1_69_0\boost/stacktrace\safe_dump_to.hpp:124:12  
                                                                                                signalHandler  
                                                                                                ...\AndroidStudioProjects\StackTraceTest\app\src\main\cpp\hello-jni.cpp:40:0  
#09 0x0001895c /system/lib/libc.so  
#10 0x0004a128 /system/lib/libc.so (tgkill+8)  
#11 0x00006ac9 /data/app/.../lib/arm/libstacktrace-test.so (myRaise+34)  
                                                                                                myRaise  
                                                                                                ...\AndroidStudioProjects\StackTraceTest\app\src\main\cpp\hello-jni.cpp:33:5  
#12 0xfffffffd <unknown>  
```  
  
This https://android.googlesource.com/platform/bionic/+log/refs/heads/master/libc/arch-arm/bionic/__restore.S doesn't fill me with confidence about stack unwinders on arm android either.

---

## Comment 7

> Username: apolukhin  
> Created at: 2020-12-15 13:56:50 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/71#issuecomment-745301501  

In https://github.com/boostorg/stacktrace/commit/66aba44f79f73d0e53da08fc9543d4ff90314ddf added a big warning that stacktraces in signal handlers are not quite supported on most of the platforms.
