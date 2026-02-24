# #6 Update platform.hpp [Closed]

> Username: akumta  
> Created at: 2016-09-14 16:40:20 UTC  
> Updated at: 2016-09-20 18:22:41 UTC  
> Closed at: 2016-09-14 22:25:58 UTC  
> Url: https://github.com/boostorg/atomic/pull/6  

See ticket #12457

---

## Comment 1

> Username: Lastique  
> Created_at: 2016-09-14 16:48:30 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247077980  

Could you please describe your changes? In particular I'm interested why you removed `__sparc_v9__` check.

---

## Comment 2

> Username: akumta  
> Created_at: 2016-09-14 17:17:57 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247087879  

The **sparc_v9** check will work only for 64 bit compilation, not for 32   
bit compilation.  
$CC -m32 1.cc -c -xdumpmacros 2> dump_m32.txt  
$ grep **sparc dump_m32.txt  
#define __sparcv8 1  
#define __sparcv8plus 1  
#define __sparc 1  
#define __sparc** 1  
$ CC -m64 1.cc -c -xdumpmacros 2> dump_m64.txt  
bash-4.1$ grep **sparc dump_m64.txt  
#define __sparcv9 1  
#define __sparc_v9** 1  
#define **sparc 1  
#define __sparc** 1  
bash-4.1$  
  
Thanks.  
  
On 09/14/16 09:48, Andrey Semashev wrote:  
  
> Could you please describe your changes? In particular I'm interested   
> why you removed |**sparc_v9**| check.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/atomic/pull/6#issuecomment-247077980, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AKUg5gNjHo8iX2iqqYvo1dr5OrXxT2wCks5qqCVegaJpZM4J8_Hy.

---

## Comment 3

> Username: Lastique  
> Created_at: 2016-09-14 17:25:56 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247090377  

But the implementation does seem to require a 64-bit mode, as it uses `casx` instruction. I can't remember now if that means only SPARCv9 and later is supported or not, it needs to be confirmed by the manuals.

---

## Comment 4

> Username: Lastique  
> Created_at: 2016-09-14 17:44:33 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247096515  

Also, according to [this](https://docs.oracle.com/cd/E60778_01/html/E68116/gqexw.html) Oracle Studio 12.5 does support both `__sync` and `__atomic` intrinsics. They are either misdetected by platform.hpp or are not enabled (probably because you don't seem to set the target CPU that supports the atomic instructions).

---

## Comment 5

> Username: Lastique  
> Created_at: 2016-09-14 18:32:54 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247111365  

I just confirmed that [SPARCv8](https://www.google.ru/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwiBtc-IvY_PAhXqHpoKHSU7BPcQFggcMAA&url=http%3A%2F%2Fwww.gaisler.com%2Fdoc%2Fsparcv8.pdf&usg=AFQjCNE3zLr9YNUkzBmqWQ9LGQzs12Q4zQ&sig2=-He569ikmkp6Wig9cElSgQ&bvm=bv.132479545,d.bGs) does not support `cas` and `casx` instructions. Boost.Atomic requires at least SPARCv9. You should adjust your compiler flags to target SPARCv9 if you want need native atomics.  
  
Regarding the change for x86. As I said earlier, it should be handled by the backend based on the `__atomic` intrinsics. Can you provide the list of all predefined macros when you target x86?

---

## Comment 6

> Username: akumta  
> Created_at: 2016-09-14 18:49:02 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247115989  

$CC -c 1.cc -xdumpmacros -m32 2> dump_m32_x86.txt  
$ more dump_m32_x86.txt |grep i386  
#define **i386 1  
#define __i386** 1  
#define i386 1  
$CC -c 1.cc -xdumpmacros -m64 2> dump_m64_x86.txt  
$ more dump_m64_x86.txt |grep 64  
#define _LARGEFILE64_SOURCE 1  
#define __x86_64 1  
#define __x86_64__ 1  
#define **amd64 1  
#define __amd64** 1  
#define _LP64 1  
#define __LP64__ 1  
$  
  
On 09/14/16 11:32, Andrey Semashev wrote:  
  
> I just confirmed that SPARCv8   
> https://www.google.ru/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwiBtc-IvY_PAhXqHpoKHSU7BPcQFggcMAA&url=http%3A%2F%2Fwww.gaisler.com%2Fdoc%2Fsparcv8.pdf&usg=AFQjCNE3zLr9YNUkzBmqWQ9LGQzs12Q4zQ&sig2=-He569ikmkp6Wig9cElSgQ&bvm=bv.132479545,d.bGs   
> does not support |cas| and |casx| instructions. Boost.Atomic requires   
> at least SPARCv9. You should adjust your compiler flags to target   
> SPARCv9 if you want need native atomics.  
>   
> Regarding the change for x86. As I said earlier, it should be handled   
> by the backend based on the |__atomic| intrinsics. Can you provide the   
> list of all predefined macros when you target x86?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/atomic/pull/6#issuecomment-247111365, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AKUg5hHCAqyDIWrfAHp22krP1ThJbKUnks5qqD3WgaJpZM4J8_Hy.

---

## Comment 7

> Username: Lastique  
> Created_at: 2016-09-14 19:25:04 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247125865  

That is not enough. Please, provide **all** predefined macros. You can attach the output of these commands to the ticket, if you like.

---

## Comment 8

> Username: akumta  
> Created_at: 2016-09-14 19:25:31 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247126013  

Would you want me to resubmit the fix with the |**sparc_v9** reverted back?  
  
|  
|  
  
> I just confirmed that SPARCv8   
> https://www.google.ru/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwiBtc-IvY_PAhXqHpoKHSU7BPcQFggcMAA&url=http%3A%2F%2Fwww.gaisler.com%2Fdoc%2Fsparcv8.pdf&usg=AFQjCNE3zLr9YNUkzBmqWQ9LGQzs12Q4zQ&sig2=-He569ikmkp6Wig9cElSgQ&bvm=bv.132479545,d.bGs   
> does not support |cas| and |casx| instructions. Boost.Atomic requires   
> at least SPARCv9. You should adjust your compiler flags to target   
> SPARCv9 if you want need native atomics.  
>   
> Regarding the change for x86. As I said earlier, it should be handled   
> by the backend based on the |__atomic| intrinsics. Can you provide the   
> list of all predefined macros when you target x86?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/atomic/pull/6#issuecomment-247111365, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AKUg5hHCAqyDIWrfAHp22krP1ThJbKUnks5qqD3WgaJpZM4J8_Hy.

---

## Comment 9

> Username: Lastique  
> Created_at: 2016-09-14 19:29:06 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247127022  

The `__sparc_v9__` part is definitely wrong. The x86 part I'm not sure yet until I see predefined macros.

---

## Comment 10

> Username: akumta  
> Created_at: 2016-09-14 19:48:16 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247132038  

I've attached the files to the ticket.  
  
On 09/14/16 12:29, Andrey Semashev wrote:  
  
> The |**sparc_v9**| part is definitely wrong. The x86 part I'm not sure   
> yet until I see predefined macros.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/atomic/pull/6#issuecomment-247127022, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AKUg5tOgG9UCac5xOvtNbhgTAoFMsfeUks5qqEsDgaJpZM4J8_Hy.

---

## Comment 11

> Username: Lastique  
> Created_at: 2016-09-14 22:25:58 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247176168  

Thank you.  
  
It looks like the compiler does not define any macros to detect the size of the natively supported atomic operations. It also does not allow to deduce the target CPU arch for x86.  
  
I've committed a few changes to develop so that the compiler should be using asm backends for gcc. For x86 dcas and mfence are assumed to be supported by default. I've also enabled support for SPARCv8+ as apparently it's the same as SPARCv9 but for 32 bits.

---

## Comment 12

> Username: akumta  
> Created_at: 2016-09-14 23:32:28 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247188724  

Hmm. Looks like I accidentally uploaded the wrong files :-(. I apologize   
for this.  
I will update the ticket with the correct files. So so sorry.  
  
On 09/14/16 15:25, Andrey Semashev wrote:  
  
> Thank you.  
>   
> It looks like the compiler does not define any macros to detect the   
> size of the natively supported atomic operations. It also does not   
> allow to deduce the target CPU arch for x86.  
>   
> I've committed a few changes to develop so that the compiler should be   
> using asm backends for gcc. For x86 dcas and mfence are assumed to be   
> supported by default. I've also enabled support for SPARCv8+ as   
> apparently it's the same as SPARCv9 but for 32 bits.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/atomic/pull/6#issuecomment-247176168, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AKUg5tFffJyF3UzO59PFd-uEc_MYwuffks5qqHR3gaJpZM4J8_Hy.

---

## Comment 13

> Username: Lastique  
> Created_at: 2016-09-15 08:41:17 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247270578  

The new files contain many more macros, including some that are defined by Boost and system headers. They also contain the `__GCC_ATOMIC` macros that I was looking for. Could you clarify how you created these files?

---

## Comment 14

> Username: akumta  
> Created_at: 2016-09-15 15:27:43 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247361562  

Initially when I sent out the list of predefined macros for each   
platform, I thought I was compiling the same source  as in the ticket.  
Only after I saw your comment that you didn't see the predefined atomic   
macros, I realized something was a miss. So the new files so contain   
more macros as they are compiled with the sources from the ticket.  
  
Btw, I submit the Oracle Developer Studio Nightly regression results, in   
another thread you mention them to have a long turn around time.Thats   
because of system resources. I do submit atleast a few runs each day.  
  
On 09/15/16 01:41, Andrey Semashev wrote:  
  
> The new files contain many more macros, including some that are   
> defined by Boost and system headers. They also contain the   
> |__GCC_ATOMIC| macros that I was looking for. Could you clarify how   
> you created these files?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/atomic/pull/6#issuecomment-247270578, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AKUg5mWyPmmDy5RJHErRNw7dlwmNXQA2ks5qqQStgaJpZM4J8_Hy.

---

## Comment 15

> Username: Lastique  
> Created_at: 2016-09-15 15:54:12 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247369604  

> Initially when I sent out the list of predefined macros for each platform, I thought I was compiling the same source as in the ticket. Only after I saw your comment that you didn't see the predefined atomic macros, I realized something was a miss. So the new files so contain more macros as they are compiled with the sources from the ticket.  
  
What I called "predefined macros" are the macros that the compiler defines **by itself** before any source is processed. And in that sense the output of `$CC -xdumpmacros` for an empty source file is the output I was looking for. I assumed that that was what you initially attached, is that right?  
  
In your latest files there are `__GCC_ATOMIC` macros, and as I suspect they are defined not by the compiler **itself** but by some headers that get included while building Boost.Log. I don't know what header defines them and whether I can trust those definitions to use them in Boost.Atomic. It would be nice to know that - let me know if you can find that out. If that header is part of the compiler I could probably include it and switch to the `__atomic` backend in Boost.Atomic. This would be more preferable than using inline asm, which is done now after my latest changes.  
  
> Btw, I submit the Oracle Developer Studio Nightly regression results, in another thread you mention them to have a long turn around time.Thats because of system resources. I do submit atleast a few runs each day.  
  
Ok, thanks. Thing is, Boost 1.62 release is nearing and I would like to have some confirmation that my latest changes at least didn't break anything or more preferably fixed the problem with Oracle Studio. The test results in Boost test matrix do update with a long delay (some of the results I see now are more than 2 weeks old), so I don't think the regular tests will make it in time by the end of the week. Could you run the tests on the current develop, at least selectively, to see what changed?

---

## Comment 16

> Username: Lastique  
> Created_at: 2016-09-15 16:01:33 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247371612  

I should add that I also modified Boost.Log to not fail completely if Boost.Atomic does not support 32-bit atomic ops. So the test results will definitely change and most likely not include the error about Boost.Atomic.  
  
What is expected is (a) both Boost.Atomic and Boost.Log build successfully and (b) the "- native-atomic-int32-supported :" line in the beginning of the test log says "yes". This would mean Boost.Atomic is providing 32-bit atomics and Boost.Log is using them.

---

## Comment 17

> Username: akumta  
> Created_at: 2016-09-15 20:09:15 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247438872  

On 09/15/16 08:54, Andrey Semashev wrote:  
  
> ```  
> Initially when I sent out the list of predefined macros for each  
> platform, I thought I was compiling the same source as in the  
> ticket. Only after I saw your comment that you didn't see the  
> predefined atomic macros, I realized something was a miss. So the  
> new files so contain more macros as they are compiled with the  
> sources from the ticket.  
> ```  
>   
> What I called "predefined macros" are the macros that the compiler   
> defines _by itself_ before any source is processed. And in that sense   
> the output of |$CC -xdumpmacros| for an empty source file is the   
> output I was looking for. I assumed that that was what you initially   
> attached, is that right?  
>   
> Yes, that is what I initially attached.  
> It should have been $CC -std=c++11 |-xdumpmacros|  
>   
> In your latest files there are |__GCC_ATOMIC| macros, and as I suspect   
> they are defined not by the compiler _itself_ but by some headers that   
> get included while building Boost.Log. I don't know what header   
> defines them and whether I can trust those definitions to use them in   
> Boost.Atomic. It would be nice to know that - let me know if you can   
> find that out. If that header is part of the compiler I could probably   
> include it and switch to the |__atomic| backend in Boost.Atomic. This   
> would be more preferable than using inline asm, which is done now   
> after my latest changes.  
  
When compiling with -std=c++11/-std=c++03, __GCC_ATOMIC macros are   
defined pretty much by the compiler itself. (Technically it is   
implemented by a hidden header that is always included).  
  
$CC -std=c++11 test.cc -xdumpmacros 2> dumpmacro.txt  
$ cat dumpmacro.txt |grep __GCC_ATOMIC  
#define __GCC_ATOMIC_BOOL_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR16_T_LOCK_FREE 2  
#define __GCC_ATOMIC_CHAR32_T_LOCK_FREE 2  
#define __GCC_ATOMIC_WCHAR_T_LOCK_FREE 2  
#define __GCC_ATOMIC_SHORT_LOCK_FREE 2  
#define __GCC_ATOMIC_INT_LOCK_FREE 2  
#define __GCC_ATOMIC_LONG_LOCK_FREE 2  
#define __GCC_ATOMIC_LLONG_LOCK_FREE 2  
#define __GCC_ATOMIC_POINTER_LOCK_FREE 2  
#define __GCC_ATOMIC_TEST_AND_SET_TRUEVAL 1  
$ cat test.cc  
$  
  
> ```  
> Btw, I submit the Oracle Developer Studio Nightly regression  
> results, in another thread you mention them to have a long turn  
> around time.Thats because of system resources. I do submit atleast  
> a few runs each day.  
> ```  
>   
> Ok, thanks. Thing is, Boost 1.62 release is nearing and I would like   
> to have some confirmation that my latest changes at least didn't break   
> anything or more preferably fixed the problem with Oracle Studio. The   
> test results in Boost test matrix do update with a long delay (some of   
> the results I see now are more than 2 weeks old), so I don't think the   
> regular tests will make it in time by the end of the week. Could you   
> run the tests on the current develop, at least selectively, to see   
> what changed?  
>   
> Yes, I can do that.  
> Thanks for all your help in seeing this through specially given that the   
> Boost 1.62 release is fast approaching.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/atomic/pull/6#issuecomment-247369604, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AKUg5gieyjqwRFQ4VG-4u_F1yURh99VYks5qqWokgaJpZM4J8_Hy.

---

## Comment 18

> Username: Lastique  
> Created_at: 2016-09-15 22:44:00 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-247475672  

I see, so it defines the macros but only in C++11 mode. I guess I could modify platform.hpp to make use of the macros when they are defined, although it looks like this way DCAS support won't be detected (the compiler does not define `__GCC_HAVE_SYNC_COMPARE_AND_SWAP_8` (even on 64-bit targets) or `__GCC_HAVE_SYNC_COMPARE_AND_SWAP_16`). Working around that will require more hacking and testing and I'm not comfortable doing that this close to the release. I think we can settle with the asm backends for 1.62 (assuming it works) and postpone further work after the release.

---

## Comment 19

> Username: akumta  
> Created_at: 2016-09-20 18:15:15 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-248385879  

On 09/15/16 08:54, Andrey Semashev wrote:  
  
> ```  
> Initially when I sent out the list of predefined macros for each  
> platform, I thought I was compiling the same source as in the  
> ticket. Only after I saw your comment that you didn't see the  
> predefined atomic macros, I realized something was a miss. So the  
> new files so contain more macros as they are compiled with the  
> sources from the ticket.  
> ```  
>   
> What I called "predefined macros" are the macros that the compiler   
> defines _by itself_ before any source is processed. And in that sense   
> the output of |$CC -xdumpmacros| for an empty source file is the   
> output I was looking for. I assumed that that was what you initially   
> attached, is that right?  
>   
> In your latest files there are |__GCC_ATOMIC| macros, and as I suspect   
> they are defined not by the compiler _itself_ but by some headers that   
> get included while building Boost.Log. I don't know what header   
> defines them and whether I can trust those definitions to use them in   
> Boost.Atomic. It would be nice to know that - let me know if you can   
> find that out. If that header is part of the compiler I could probably   
> include it and switch to the |__atomic| backend in Boost.Atomic. This   
> would be more preferable than using inline asm, which is done now   
> after my latest changes.  
>   
> ```  
> Btw, I submit the Oracle Developer Studio Nightly regression  
> results, in another thread you mention them to have a long turn  
> around time.Thats because of system resources. I do submit atleast  
> a few runs each day.  
> ```  
>   
> Ok, thanks. Thing is, Boost 1.62 release is nearing and I would like   
> to have some confirmation that my latest changes at least didn't break   
> anything or more preferably fixed the problem with Oracle Studio. The   
> test results in Boost test matrix do update with a long delay (some of   
> the results I see now are more than 2 weeks old), so I don't think the   
> regular tests will make it in time by the end of the week. Could you   
> run the tests on the current develop, at least selectively, to see   
> what changed?  
>   
> Just checking in to see if the changes would be able to go in for Boost   
> 1.62. I think the deadline is fast approaching. Thanks.  
> I did modify the cronjobs over the weekend to run tests against develop.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/atomic/pull/6#issuecomment-247369604, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AKUg5gieyjqwRFQ4VG-4u_F1yURh99VYks5qqWokgaJpZM4J8_Hy.

---

## Comment 20

> Username: Lastique  
> Created_at: 2016-09-20 18:22:41 UTC  
> Url: https://github.com/boostorg/atomic/pull/6#issuecomment-248387970  

I did merge the changes to master. The SPARC testers look green, but apparently some Intel tests started failing. I can't see what's wrong with them because the test log contains only the exit code.  
  
http://www.boost.org/development/tests/develop/developer/output/oracle-intel-S2-12-5_next-cpp11-boost-bin-v2-libs-atomic-test-ordering-test-sun-12-5_next_cpp11-release-threading-multi.html  
http://www.boost.org/development/tests/develop/developer/output/oracle-intel-S2-12-5_next-cpp11-boost-bin-v2-libs-atomic-test-native_api-test-sun-12-5_next_cpp11-release-threading-multi.html  
  
Do you know what that means?  
  
I think 1.62 will have to do with the current version anyway - it's too late now.

---
