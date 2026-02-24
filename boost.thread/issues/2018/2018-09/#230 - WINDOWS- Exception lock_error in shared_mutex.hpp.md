# #230 - WINDOWS: Exception lock_error in shared_mutex.hpp [Open]

> Username: caroline-clover  
> Created at: 2018-09-03 07:14:46 UTC  
> Updated at: 2019-06-20 22:18:25 UTC  
> Url: https://github.com/boostorg/thread/issues/230  

Hi,  
My testing program sometimes get exception lock_error.  
According to dump, the exception is caused by the share_waiting overflow in bool timed_lock_shared(boost::system_time const& wait_until)  
I've tried to modify the code in shared_mutex.hpp to avoid getting exception.  
But I'm not sure if it's the right way to fix the issue or not.  
  
Platform: Windows 10   
Boost version: 1.62  
Testing code:  
```C++  
boost::shared_mutex mtx;  
int g_cnt = 5000000;  
void f()  
{  
    while (g_cnt > 0)  
    {  
        boost::upgrade_lock<boost::shared_mutex> readlock(mtx);  
        boost::upgrade_to_unique_lock<boost::shared_mutex> writelock(readlock);  
        if (g_cnt > 0)  
            --g_cnt;  
    }  
}  
void g()  
{  
    while (g_cnt > 0)  
    {  
        boost::shared_lock<boost::shared_mutex> readlock(mtx);  
    }  
}  
void h()  
{  
    while (g_cnt > 0)  
    {  
        boost::unique_lock<boost::shared_mutex> lock(mtx);  
        if (g_cnt > 0)  
            --g_cnt;  
    }  
}  
int main()  
{  
    boost::thread t0(f);  
    boost::thread t1(g);  
    boost::thread t2(h);  
  
    t0.join();  
    t1.join();  
    t2.join();  
}  
```  
Related info in Dump:  
```  
//Stack  
....  
VCRUNTIME140!CxxThrowException+0xc2  
boost::throw_exception<boost::lock_error>+0x3f [...\boost\boost\throw_exception.hpp @ 69]   
boost::shared_mutex::timed_lock_shared+0x297 [...\boost\boost\thread\win32\shared_mutex.hpp @ 169]   
boost::shared_mutex::lock_shared+0x17 (Inline Function @ 00007ff6`980f115c) [...\boost\boost\thread\win32\shared_mutex.hpp @ 144]   
boost::shared_lock<boost::shared_mutex>::lock+0x13c [...\boost\boost\thread\lock_types.hpp @ 645]   
boost::shared_lock<boost::shared_mutex>::{ctor}+0x14 (Inline Function @ 00007ff6`981384c4) [...\boost\boost\thread\lock_types.hpp @ 520]   
g+0x34  
boost_thread_vc140_mt_1_62!boost::`anonymous namespace'::thread_start_function+0x43 [...\boost\boost_1_62_0\libs\thread\src\win32\thread.cpp @ 296]   
….  
  
//value of old_state  
@"old_state"                 [Type: boost::shared_mutex::state_data]  
    [+0x000 (10: 0)] shared_count     : 0x0  
    [+0x000 (21:11)] shared_waiting   : 0x7ff  
    [+0x000 (22:22)] exclusive        : 0x1  
    [+0x000 (23:23)] upgrade          : 0x0  
    [+0x000 (30:24)] exclusive_waiting : 0x1  
    [+0x000 (31:31)] exclusive_waiting_blocked : 0x1  
```  
Changes in shared_mutex.hpp:  
```C++  
         void release_shared_waiters(state_data old_state)  
         {  
-            if(old_state.shared_waiting || old_state.exclusive_waiting)  
+            if(old_state.shared_waiting)  
             {  
-                BOOST_VERIFY(detail::win32::ReleaseSemaphore(semaphores[unlock_sem],old_state.shared_waiting + (old_state.exclusive_waiting?1:0),0)!=0);  
+                BOOST_VERIFY(detail::win32::ReleaseSemaphore(semaphores[unlock_sem],old_state.shared_waiting,0)!=0);  
             }  
  
         }  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-09-06 04:58:50 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-418964919  

The shared_mutex implementation has a lot of issues yet :(  
I'll not have too much time to look at this in the mean time, as I 'm unable to test it now.  
  
I would like to see a new test that probes that your changes fixe something. Could you provide one?

---

## Comment 2

> Username: caroline-clover  
> Created at: 2018-09-07 03:04:57 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-419307360  

Hi viboes,  
I'm not sure what you ask me to provide.  
Could you kindly explain it? Thanks.

---

## Comment 3

> Username: viboes  
> Created at: 2018-09-15 04:19:59 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-421529475  

I would like that you provide a PR with a test that shows that there is an issue.  
  
Then you could add your proposed change in a PR and show that you are fixing the test and don't introducing any regression.

---

## Comment 4

> Username: viboes  
> Created at: 2018-09-15 04:22:43 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-421529606  

Ah, please, could you try also with std::shared_mutex and std::thread and see how it behaves.

---

## Comment 5

> Username: viboes  
> Created at: 2018-10-09 04:47:47 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-428058322  

Shouldn't  `g_cnt` be read protected  
  
```  
    boost::upgrade_lock<boost::shared_mutex> readlock(mtx);  
    while (g_cnt > 0)  
    {  
```

---

## Comment 6

> Username: viboes  
> Created at: 2018-10-09 04:51:40 UTC  
> Updated at: 2018-10-09 04:52:40 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-428058788  

The code has changed a lot since 1.62.  
Please could you try with the develop branch or the last version?

---

## Comment 7

> Username: viboes  
> Created at: 2018-10-09 04:54:08 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-428059229  

Set to invalid, waiting for more information concerning the result with version 1.68 or later.

---

## Comment 8

> Username: Kojoley  
> Created at: 2018-10-24 20:21:48 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-432813914  

> Shouldn't `g_cnt` be read protected  
> Set to invalid, waiting for more information concerning the result with version 1.68 or later.  
  
This also throws (on the latest develop):  
  
```cpp  
#include <boost/thread.hpp>  
  
boost::shared_mutex mtx;  
int g_cnt = 5000000;  
void f()  
{  
    for (;;) {  
        boost::upgrade_lock<boost::shared_mutex> readlock(mtx);  
        boost::upgrade_to_unique_lock<boost::shared_mutex> writelock(readlock);  
        if (g_cnt > 0)  
            --g_cnt;  
        else  
            break;  
    }  
}  
void g()  
{  
    for (;;) {  
        boost::shared_lock<boost::shared_mutex> readlock(mtx);  
        if (g_cnt <= 0) break;  
    }  
}  
void h()  
{  
    for (;;) {  
        boost::unique_lock<boost::shared_mutex> lock(mtx);  
        if (g_cnt > 0)  
            --g_cnt;  
        else  
            break;  
    }  
}  
int main()  
{  
    boost::thread t0(f);  
    boost::thread t1(g);  
    boost::thread t2(h);  
  
    t0.join();  
    t1.join();  
    t2.join();  
}  
```  
  
So the bug report is valid. Also notice that shared mutex tests are timeouting on appveyor from time to time, it also indicates that there is a problem in shared mutexes.

---

## Comment 9

> Username: viboes  
> Created at: 2018-10-24 21:19:45 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-432832121  

You have not answered to my question.  
Shouldn't g_cnt be read protected?  
Without an answer I will move it again to invalid.  
  
The issues with timeouts have nothing to be with a bug, but with the unstability of the test. No test managing real time can be stable in all the environments.

---

## Comment 10

> Username: viboes  
> Created at: 2018-10-24 21:20:00 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-432832206  

BTW, any fix is welcome.

---

## Comment 11

> Username: Kojoley  
> Created at: 2018-10-24 21:23:11 UTC  
> Updated at: 2018-10-24 21:45:20 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-432833184  

I'm not an author of the issue, I can't say for him. I posted a version with protected variable and I tested it for you and verified that it fails on the assertion.

---

## Comment 12

> Username: viboes  
> Created at: 2018-10-25 01:44:36 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-432884575  

Sorry. I didn't see that the example was modified :(

---

## Comment 13

> Username: austin-beer  
> Created at: 2019-03-23 19:51:38 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-475899382  

As another data point, using the code @Kojoley provided, I was unable to reproduce this issue using the following environment:  
  
Boost: latest develop branch  
Compiler: Visual Studio 2017 64-bit (15.3.4, 19.11.25508.2)  
OS: Windows 7 SP1 running within VMWare Workstation

---

## Comment 14

> Username: Kojoley  
> Created at: 2019-03-23 21:17:35 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-475905518  

Still reproducible for me  
  
```  
.\b2 -j2 variant=debug threading=multi link=static address-model=64 toolset=msvc-14.1 libs/thread/build//stage  
cl repro.cpp -I. /MD /EHsc /link /LIBPATH:stage/lib && repro.exe  
```  
  
Boost: latest develop branch  
Compiler: Microsoft (R) C/C++ Optimizing Compiler Version 19.16.27027.1 for x64 (Microsoft Visual Studio Community 2017 Version 15.9.8 VisualStudio.15.Release/15.9.8+28307.481)  
OS: Microsoft Windows [Version 6.1.7601]

---

## Comment 15

> Username: austin-beer  
> Created at: 2019-03-23 23:23:45 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-475913351  

Okay, using a debug instead of a release build, and running the test executable many times, I'm able to occasionally reproduce this issue.

---

## Comment 16

> Username: Kojoley  
> Created at: 2019-03-23 23:25:56 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-475913466  

It reproduces for me in any variant debug/release static/shared on the first try.

---

## Comment 17

> Username: austin-beer  
> Created at: 2019-03-23 23:27:26 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-475913557  

Since this is probably a timing-related issue, it might not occur as frequently for me because I'm running Windows inside of a VM.

---

## Comment 18

> Username: viboes  
> Created at: 2019-03-24 08:12:53 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-475937761  

If you don't mind of the performances, the implementation used when you define BOOST_THREAD_V2_SHARED_MUTEX is more robust.  
  
I'll appreciate if you could give a try and let us know if you have the same kind of issue.

---

## Comment 19

> Username: oenayet  
> Created at: 2019-06-20 21:06:54 UTC  
> Updated at: 2019-06-20 21:10:11 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-504183017  

@viboes  I reproduced the same issue . Below are my environment details:  
Windows Server 2012 64-bit  
Visual Studio 2010 Compiler  
Visual Studio 2017 IDE  
64-bit Process  
Boost 1.57 & 1.70  
  
The issue is easily reproducible, just when calling lock_shared() & unlock_shared() from 1 thread many consecutive times. Crash happens in timed_lock_shared():  
`                        ++new_state.shared_count;  
                        if(!new_state.shared_count)  
                        {  
                            boost::throw_exception(boost::lock_error());  -> this line  
                        }`  
  
I managed to make a work around by not using the win32 version of shared_mutex but instead use the pthread version, this can be done by adding the following line of code before including shared_mutex.hpp file:  
  
`#define BOOST_THREAD_PROVIDES_GENERIC_SHARED_MUTEX_ON_WIN`

---

## Comment 20

> Username: austin-beer  
> Created at: 2019-06-20 22:18:25 UTC  
> Url: https://github.com/boostorg/thread/issues/230#issuecomment-504219931  

I've been working on updates to the v2/shared_mutex.hpp implemented to ensure that it is both correct and fast on both POSIX and Windows. Once those updates are completed I believe that it could be used to replace the existing implementations on both POSIX and Windows, which would resolve this issue. However, I'm waiting until the following issue is resolved before moving forward.  
  
https://github.com/HowardHinnant/upgrade_mutex/issues/2
