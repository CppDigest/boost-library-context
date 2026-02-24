# #28 - boost::atomic<T>::wait (P1135R5, C++20) [Closed]

> Username: AlexGuteniev  
> Created at: 2020-02-26 07:23:33 UTC  
> Updated at: 2020-07-19 16:21:38 UTC  
> Closed at: 2020-06-06 21:13:11 UTC  
> Url: https://github.com/boostorg/atomic/issues/28  

I want to have wait, notify_one and notify_all added to boost::atomic, as defined in [P1135R5](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1135r5.html) proposal.  
  
Also would be nice to have an extension:  
instead of returning void, wait could return observed new value.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-26 07:55:00 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-591288821  

Yes, I'm planning to add those.  
  
I see no point in returning a value from `wait` because that value will always be equal to the value you pass as an argument.

---

## Comment 2

> Username: AlexGuteniev  
> Created at: 2020-02-26 08:01:27 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-591291140  

Unlike CAS, `wait` returns when value is _different_ from the passed one, so I do see a point.  
  
See [reply](https://github.com/brycelelbach/wg21_p1135_cpp20_synchronization_library/issues/14#issuecomment-591117318) of `wait` proposal author.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-02-26 08:14:57 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-591295966  

> Unlike CAS, wait returns when value is different from the passed one  
  
The way I read N4849 [atomics.wait], `wait` should only return when the stored value is equal to the passed value. If the stored value is only equal momentarily, `wait` may not return at all and continue blocking. That doesn't mean your next `load` won't return a different value, but that is not fixable in `wait`.  
  
I could add `wait` without an argument that would return for all notifications and spurious wakeups, but it would have no need to reload the value upon unblocking, so you'd have to `load` anyway.

---

## Comment 4

> Username: AlexGuteniev  
> Created at: 2020-02-26 08:23:12 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-591299021  

In P1135R5 it is stated as follows:  
  
> void wait(T old, memory_order order = memory_order::seq_cst) const volatile noexcept;  
> void wait(T old, memory_order order = memory_order::seq_cst) const noexcept;  
> Expects: order is neither memory_order::release nor memory_order::acq_rel.  
> Effects: Repeatedly performs the following steps, in order:  
> Evaluates load(order) and compares its value representation for equality against that of old.  
>   
> If they compare unequal, returns.  
>   
> Blocks until it is unblocked by an atomic notifying operation or is unblocked spuriously.  
  
Both WaitOnAddress  on Windows and futex on Linux (which are assumed to be non-fallback implementations) behave this way. Generally I see `wait` that would wait for specific value as far less useful than `wait` for mismatch

---

## Comment 5

> Username: Lastique  
> Created at: 2020-02-26 08:29:26 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-591301402  

Right, which means `wait` only returns when the value matches the argument.  
  
Generally, I see the `wait` without an argument as much more useful than either matching or mismatching one. Also, I think atomics is the wrong place to integrate futexes. The standardized interface is too limiting so people who really need futexes (and can't use e.g. condition variables) will likely have to use them directly anyway.

---

## Comment 6

> Username: Lastique  
> Created at: 2020-02-26 08:32:59 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-591302796  

> If they compare *unequal*, returns.  
  
I misread the *unequal* part. Ok, it makes sense now.

---

## Comment 7

> Username: AlexGuteniev  
> Created at: 2020-02-26 09:52:57 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-591338041  

> Also, I think atomics is the wrong place to integrate futexes.  
  
Sure, it is up to you how do you implement this feature. But I'd use `WaitOnAddress` on Windows when available, and made this availability decision at runtime (`GetProcAddress` import).

---

## Comment 8

> Username: Lastique  
> Created at: 2020-06-06 21:13:11 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-640118843  

Implemented in develop.

---

## Comment 9

> Username: AlexGuteniev  
> Created at: 2020-06-07 04:15:26 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-640154343  

Thanks, looks good!  
  
Have you considered _timed wait_ as an extension?  
I don't have a current need for timed primitives, but they might be helpful.  
  
 It is beyond the standard but probably still worth considering:  
 - atomic wait can be used to implement any other non-timed synchronization primitive I can think of on top of it, whereas when _timed wait_ is added, any primitive can be implemented, including timed.  
 - Implementation complexity added cost is small, comparing to the rest of the feature. Futexes, semaphores, condition variables support timeout. Only need to convert it correctly from passed duration and make sure that in case of spurious wake only remaining timeout is used for retry.  
  
The only downside of having timed wait I see would be chrono dependency.

---

## Comment 10

> Username: Lastique  
> Created at: 2020-06-07 09:15:38 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-640182475  

Yes, I thought about timed versions, but I haven't decided on the interface. The current `wait` returns the loaded value, and the timed functions will have to return either `bool` or an equivalent of `std::cv_status`. It adds inconsistency and it's not clear how to return the loaded value. It would be unfortunate if a different version would be standardized later.  
  
The dependency on chrono, I think, can be managed, but it won't be trivial.  
  
On the implementation side, there will be a problem for Windows semaphores version, as it would add the possibility of the semaphore count going out of sync with the waiter count. I can probably work around it, but I didn't think of it too much. I need to double check that all platforms support timed operations.

---

## Comment 11

> Username: AlexGuteniev  
> Created at: 2020-06-07 10:11:56 UTC  
> Updated at: 2020-06-07 10:46:36 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-640191036  

I would propose a structure, first member is observed value, second is `std::cv_status`. Like `std::map::insert` returing a pair. And probably another method name, like `wait_for` / `wait_until`, to make usual `wait` returning only value.   
EDIT: `<charconv>` is a more recent example of this, again a structure, first member is the value result, second is status code.  
  
Semaphore going out of sync would probably produce spurios wakes for subsequent waits, and I think that's acceptable and has to be handled on the same level where `WaitOnAddress` spurious wakes are handled.  
  
If chrono dependency is too complex, just having `wait_for` with satisfactory resolution duration passed as an integer or a couple of integers is good enough. Maybe it makes sense to have higher than milli resolution, even if that's not currently supported on Windows, in other platform or in future it may be supported.  
  
> It would be unfortunate if a different version would be standardized later.  
  
This risk can be partly mitigated by making a proposal out of `boost::atomic`, before anyone else has a different idea :-)

---

## Comment 12

> Username: AlexGuteniev  
> Created at: 2020-06-09 11:51:20 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641239663  

I want to warn about `GetModuleHandle(L"KernelBase.dll")`.  
  
If you compile this program:  
```  
#include <windows.h>  
  
#pragma comment(lib, "Synchronization.lib")  
  
int main()  
{  
    int v = 0;  
    WakeByAddressAll(&v);  
}  
```  
and run `dumpbin /IMPORTS` on it, you'll observe that `api-ms-win-core-synch-l1-2-0.dll` is used as DLL name for `WakeByAddressAll`.  
  
This is ApiSet DLL that forwards to the actual DLL. Since the feature is available starting Windows 8, ApiSet is available whereever the feature is available.  
  
MSVC STL maintainer told that the ApiSet DLL is the only correct DLL. See https://github.com/microsoft/STL/pull/593#issuecomment-641019129:  
> Then Boost will break on NanoServer, XBox, etc. That not being the correct DLL is not merely a hypothetical concern, shipping platforms today do not do that.  
  
----  
  
There is also another concern. There's a question if `KernelBase.dll` or whatever else `api-ms-win-core-synch-l1-2-0.dll` forwards to is loaded on process start and never unloads. It is not `ntdll.dll`.

---

## Comment 13

> Username: Lastique  
> Created at: 2020-06-09 12:16:38 UTC  
> Updated at: 2020-06-09 12:21:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641250730  

In my tests, `KernelBase.dll` was loaded in the process. I don't know by what means.  
  
I've asked about `api-ms-win-core-synch-l1-2-0.dll` name stability in the PR you referenced. Depending on the answer, I might or might not change Boost.Atomic.  
  
PS: MS is doing something funny with `WaitOnAddress` & other functions. They are not marked `__dllimport`, so I'm not sure how exactly they are linked. There's probably non-import symbols in `Synchronization.lib` that forward to some dll in runtime, but it's not clear which one and why they did that.

---

## Comment 14

> Username: AlexGuteniev  
> Created at: 2020-06-09 12:35:20 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641260024  

> In my tests, `KernelBase.dll` was loaded in the process. I don't know by what means.  
  
This is enough for my purposes, when I target one application, one system. This might be not enough for a library that attempts to target multiple programs running on multiple operating systems, including future ones.  
  
----  
  
`__declspec(dllimport)` is optional for an imported function. `Synchronization.lib` imports look like usual imports (except that they import from ApiSet DLL `api-ms-win-core-synch-l1-2-0.dll` instead of a "normal" DLL)  
  
Future OS I think supposed to be solved by adding, say, `api-ms-win-core-synch-l1-2-1.dll`, but still keeping `api-ms-win-core-synch-l1-2-0.dll` as a forwarder.   
  
----  
  
It is really bad situation that documentation mentions `Kernel32.dll` for those APIs, instead of the right DLL. I proposed a PR to change it to ApiSet DLL, but it got stuck. Though documentation maitnainer confirmed the ApiSet DLL is the right one: https://github.com/MicrosoftDocs/sdk-api/pull/211#pullrequestreview-396798919.

---

## Comment 15

> Username: Lastique  
> Created at: 2020-06-09 15:26:43 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641374528  

> `__declspec(dllimport)` is optional for an imported function.  
  
Something must have changed in how linking on Windows works, since as I remember `__declspec(dllimport)` mangled the symbol differently, and the mangled symbol was present in the .lib.  
  
> Future OS I think supposed to be solved by adding, say, api-ms-win-core-synch-l1-2-1.dll, but still keeping api-ms-win-core-synch-l1-2-0.dll as a forwarder.  
  
I'm not sure how that's going to work. I don't imagine a process will keep loading the old library if it was linked against the new one.

---

## Comment 16

> Username: AlexGuteniev  
> Created at: 2020-06-09 15:42:46 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641389505  

> Something must have changed in how linking on Windows works  
  
It has indeed changed. Now you can omit `__declspec(dllimport)`. As a proof, this works (no includes, this is the whole program):  
  
```  
extern "C" void  __stdcall Sleep(unsigned long);  
  
#pragma comment(lib, "Kernel32.lib")  
  
int main()  
{  
    Sleep(4000);  
}  
```  
  
> I'm not sure how that's going to work. I don't imagine a process will keep loading the old library if it was linked against the new one.  
  
No, only old processes will forward to new library in new OS, not reverse. So once `api-ms-win-core-synch-l1-2-0.dll` name is introduced, it will forward to the implementation, even if new functions are added to `api-ms-win-core-synch-l1-2-1.dll`.  
  
Note that maintenance cost for OS is not very high. The ApiSet DLLs are not distributed, they are just names.

---

## Comment 17

> Username: Lastique  
> Created at: 2020-06-09 16:37:04 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641420708  

> No, only old processes will forward to new library in new OS, not reverse. So once `api-ms-win-core-synch-l1-2-0.dll` name is introduced, it will forward to the implementation, even if new functions are added to `api-ms-win-core-synch-l1-2-1.dll`.  
  
What I mean is that if I have `api-ms-win-core-synch-l1-2-0.dll` hardcoded in Boost.Atomic, it will break when the user starts linking with `api-ms-win-core-synch-l1-2-1.dll`. I.e. the process that is linked against `api-ms-win-core-synch-l1-2-1.dll` will not load `api-ms-win-core-synch-l1-2-0.dll`.

---

## Comment 18

> Username: AlexGuteniev  
> Created at: 2020-06-09 18:21:23 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641489582  

I see.   
  
First, let's clarify that there's no issue in linked DLL/vs loaded DLL, Because if you don't link against `Synchronization.lib`, the DLL is not requested to load when resolving static imports. There's just no entry in `IMPORTS`.  
  
How it actually works: `GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll")` resolves to `KernelBase.dll`, and `KernelBase.dll` happens to be loaded into process.  
  
How it may be broken in future:  
1. `api-ms-win-core-synch-l1-2-0.dll` starts resolving to another DLL that is still loaded by default. Then `GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll")` is not broken, but `GetModuleHandleW(L"KernelBase.dll")`  is.  
2. `api-ms-win-core-synch-l1-2-0.dll` starts resolving to a DLL that is not loaded by default. This breaks both `GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll")` and `GetModuleHandleW(L"KernelBase.dll")`   
3. `KernelBase.dll` becomes not loaded by default. Again, this breaks both `GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll")` and `GetModuleHandleW(L"KernelBase.dll")`   
4. `GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll")` stops existing, but `KernelBase.dll` still exist. Only this way `GetModuleHandleW(L"KernelBase.dll")`  is not broken but `GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll")` is broken.  
  
I tend to think that 4 is less likely than 1.  
  
However, another side of it is consequences of using DLL that is not loaded by default:  
(a) It is not loaded, so fallback is used. Correct, but suboptimal behavior  
(b) It happens to be loaded by other means, but then it is unloaded. A crash.  
  
I think that with `GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll")` the situation (b) is more likely than with `GetModuleHandleW(L"KernelBase.dll")`  
  
I also understand that all these problems of running into (b) situation, as well as most of (a) situations are excluded by using `LoadLibrary`/`FreeLibrary`. but this brings other problems in.  
  
How do I see the solution: I want to push the information about `GetModuleHandle` safety into the documentation. https://github.com/MicrosoftDocs/sdk-api/pull/226

---

## Comment 19

> Username: Lastique  
> Created at: 2020-06-09 18:41:47 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641500015  

> `GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll")` resolves to `KernelBase.dll`, and `KernelBase.dll` happens to be loaded into process.  
  
It does not "resolve to" KernelBase.dll. api-ms-win-core-synch-l1-2-0.dll is an actual library in the filesystem your application is linked against, so `GetModuleHandleW` will return its handle. If an application is not linked against it (e.g. because it is now linked against api-ms-win-core-synch-l1-2-1.dll), `GetModuleHandleW` will fail.  
  
Regarding KernelBase.dll, I'm not sure which exact modules are linked against it, but at least api-ms-win-core-synch-l1-2-0.dll is. So it is loaded indirectly through api-ms-win-core-synch-l1-2-0.dll, which makes `GetModuleHandleW` succeed.

---

## Comment 20

> Username: AlexGuteniev  
> Created at: 2020-06-09 18:54:10 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641505955  

> It does not "resolve to" KernelBase.dll. api-ms-win-core-synch-l1-2-0.dll is  
  
It does.  
```  
#include <iostream>  
#include <Windows.h>  
  
int main()  
{  
    TCHAR path[MAX_PATH];  
    HMODULE m = GetModuleHandleW(L"api-ms-win-core-synch-l1-2-0.dll");  
    ::GetModuleFileNameW(m, path, MAX_PATH);  
    std::wcout << path;  
}  
```  
Prints `C:\WINDOWS\System32\KERNELBASE.dll`  
  
> api-ms-win-core-synch-l1-2-0.dll is an actual library in the filesystem your application  
  
This is current implementation detail of ApiSets that is likely to change in future. If you peek into implementation of the DLL, you'll notice that there's no implementation actually, this DLL does not even import other DLLs.

---

## Comment 21

> Username: AlexGuteniev  
> Created at: 2020-06-09 19:07:37 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641513146  

Currently such DLLs consist only of import forwarders:  
  
![api-ms-win-core-synch-l1-2-0](https://user-images.githubusercontent.com/3995422/84188992-2aa0f380-aa9d-11ea-9bff-dd0a63491164.png)  
  
Note that the forwarder forwards to `Kernel32.WakeByAddress*`, and these functions do not exist in `Kernel32`.   
  
Previously (when I looked at this earlier) these functions contained `return 0` implementation (`xor eax, eax`), which is also nothing useful.  
  
I think that DLLs only exist as a workaround for some specific situation where a physical file with given imports should exist; but they are not used during normal operation

---

## Comment 22

> Username: Lastique  
> Created at: 2020-06-09 19:18:58 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641519027  

> Prints `C:\WINDOWS\System32\KERNELBASE.dll`  
  
Well, that's some weird stuff, if I ever seen one. Thanks, I learned something new today.  
  
Anyway, that doesn't really change much. If at some point this resolving magic stops recognizing `api-ms-win-core-synch-l1-2-0.dll` for some reason, `GetModuleHandleW` will break.  
  
BTW, I can already see there are `api-ms-win-core-synch-l1-1-0.dll` and `api-ms-win-core-synch-l1-2-0.dll` files, so the version bump has happened at least once. Both resolve to KernelBase.dll.

---

## Comment 23

> Username: AlexGuteniev  
> Created at: 2020-06-09 20:10:48 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641543244  

More weird stuff:  
```C++  
#include <iostream>  
#include <Windows.h>  
  
int main()  
{  
    for (auto* name : { L"api-ms-win-core-synch-l1-2-71.dll",  
                        L"api-ms-win-core-synch-l1-3-14.dll" })  
    {  
        TCHAR real_name[MAX_PATH];  
        HMODULE m = GetModuleHandleW(name);  
        if (m != nullptr)  
        {  
            ::GetModuleFileNameW(m, real_name, MAX_PATH);  
            std::wcout << name << L"  resolves to  " << real_name << L"\n";  
        }  
        else  
        {  
            std::wcout << name << L"  does not resolve\n";  
        }  
    }  
}  
  
```  
  
Output:  
```  
api-ms-win-core-synch-l1-2-71.dll  resolves to  C:\WINDOWS\System32\KERNELBASE.dll  
api-ms-win-core-synch-l1-3-14.dll  does not resolve  
```  
  
Now go find `api-ms-win-core-synch-l1-2-71.dll` on your system :-)  
  
----  
  
As you see the version bump happens, and old DLL exists, it is logically to assume that with newer version the forwarding will still happen.  
  
Note that `Synchnronization.lib` will cause application to link against `api-ms-win-core-synch-l1-2-0.dll`, so if forwarding stops existing, all these apps stop working.  
  
Also as my PR you have commented is now accepted and merged, expect this page https://docs.microsoft.com/en-us/windows/win32/api/synchapi/nf-synchapi-waitonaddress change to mention `api-ms-win-core-synch-l1-2-0.dll` in a few hours.  
  
  
The real problem is if the target for `api-ms-win-core-synch-l1-2-0.dll` changes to something not automatically loaded into the process. But the same problem exists, if `KernelBase.dll` stops being automatically loaded.  
  
Both approaches rely on currently non-existent guarantee.    
  
I'm trying to acquire this guarantee by another PR: https://github.com/MicrosoftDocs/sdk-api/pull/226 . This PR also tries to acquire guarantee about **fences** in `WakeByAddress*` (which is currently assumed by `boost::atomic<T>::notify_*`, and I think the assumption is reasonable, but unfortunately not documented).

---

## Comment 24

> Username: Lastique  
> Created at: 2020-06-09 20:30:43 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641552460  

> This PR also tries to acquire guarantee about fences in WakeByAddress* (which is currently assumed by boost::atomic<T>::notify_*, and I think the assumption is reasonable, but unfortunately not documented).  
  
It is not assumed. Notifying operations do not impose memory ordering constraints.

---

## Comment 25

> Username: AlexGuteniev  
> Created at: 2020-06-10 03:24:46 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641697061  

> It is not assumed. Notifying operations do not impose memory ordering constraints.  
  
This is strange. Maybe I do not understand, or there is standard defect.  
  
I expect the below program not to hang.   
If (2) and (3) are observed in reverse order in (1), it will hang:  
  
```  
#include <atomic>  
#include <chrono>  
#include <thread>  
  
  
int main()  
{  
    std::atomic<bool> go{ false };  
  
    std::thread thd([&go] {  
        go.wait(false, std::memory_order_relaxed); // (1)  
    });  
  
    std::this_thread::sleep_for(std::chrono::milliseconds(400));  
  
    go.store(true, std::memory_order_relaxed); // (2)  
    go.notify_all();                           // (3)  
  
    thd.join();  
  
    return 0;  
}  
```  
  
I asked this on SO: https://stackoverflow.com/q/62295310/2945027

---

## Comment 26

> Username: AlexGuteniev  
> Created at: 2020-06-10 04:51:06 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641719124  

The situation with these fences is more interesting, if you take look at other implementations as well.  
  
The proposal author's implementation is different from yours in the following:  
1. It tries to avoid kernel call for `notify_all` / `notify_one`. Calls to `wait` are registered, and `notify_*` calls check if there are `wait` calls for this variable.  
2. It implements indirect `futex` use, that is if the size of variable is not 4 (the size that is accepted by `futex` function), it still uses futex wait on a proxy variable. Such futex wait is expected to be more optimal than use of condition variables, since you don't need mutexes in this case.  
  
On Windows, however, both optimization are not necessarily needed:  
1. `WakeByAddress*` perform similar check (confirmed by disassembly)  
2. `WaitOnAddress` supports sizes of 1,2,4, and 8, so all sane uses of `atomic<T>::wait` are covered  
  
Since the author made a cross-platform implementation, he implemented both optimization. In this case, even if the Standard implies no synchronization, it is still implementation's problem to put the fences, not the user's.  
  
In my PR to MSVC STL, I did not implement the first optimization. Still I've implemented the second one. Mostly because currently `atomic<3 bytes>` is not lock free, and does not have padding, but it is expected to be lock-free, so I wanted to give more decent optimization of this case. But I did not put fences, as disassebly shows that `WaitOnAddress` / `WakeByAddress` has them.  
  
----  
  
Now back to your implementation. See that it is fine on Windows, but could be suboptimal on Linux. And if you want to optimize it, you'll need to look into fences in Linux.

---

## Comment 27

> Username: Lastique  
> Created at: 2020-06-10 07:26:32 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641788894  

> If (2) and (3) are observed in reverse order in (1), it will hang  
  
All operations on a given atomic object happen in a single total order S. Given that (2) and (3) happen in the same thread, they are always in that order in S. OTOH, (1) can be in any place in S wrt. (2) and (3).  
  
All that is separate from the memory ordering constraints, which indicate ordering of *other non-atomic effects* wrt. atomic operations. In your example, a relaxed load (as part of `wait`), relaxed store and notification operation do not impose any restrictions. In a hypothetical mutex based on `atomic`, the load would have to be an acquire operation, the store - a release operation, and the notification operation would still have no memory ordering constraints. In C++ standard terms, the load would *synchronize with* the store, meaning that all memory accesses in thread A that happened before (in program order) the store by thread A become visible to thread B after it has executed the load. The load does not *synchronize with* the notification operation.

---

## Comment 28

> Username: AlexGuteniev  
> Created at: 2020-06-10 07:32:53 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641792362  

> All operations on a given atomic object happen in a single total order S. Given that (2) and (3) happen in the same thread, they are always in that order in S. OTOH, (1) can be in any place in S wrt. (2) and (3).  
  
Thanks, it answers standard part. That standard requires the code not to hang.  
  
Now, regarding implementation, what would happen inside `WaitOnAddress` or `futex`. If it runs notifying waiters without waiting when variable itself is visible across threads, then it does not implement this model, unless an external fence is added.

---

## Comment 29

> Username: Lastique  
> Created at: 2020-06-10 07:37:13 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641794705  

> It tries to avoid kernel call for notify_all / notify_one. Calls to wait are registered, and notify_* calls check if there are wait calls for this variable.  
  
This is not possible to do in a pure futex implementation.  
  
I posit this is not necessary to do as well, because the user would do this based on the atomic object value before issuing the notifying operation. That is, if the user cares about performance and such a check is possible in his code.  
  
> It implements indirect futex use, that is if the size of variable is not 4 (the size that is accepted by futex function), it still uses futex wait on a proxy variable. Such futex wait is expected to be more optimal than use of condition variables, since you don't need mutexes in this case.  
  
You do need a mutex to allocate the proxy in a pool. As far as I remember, the original code used the same proxy for multiple atomics, which necessitated `notify_one` to wake up all threads instead of one. This is terribly inefficient.

---

## Comment 30

> Username: AlexGuteniev  
> Created at: 2020-06-10 07:45:45 UTC  
> Updated at: 2020-06-10 07:54:07 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641801912  

> That is, if the user cares about performance and such a check is possible in his code.  
  
Say I'm now using `WaitOnAddress` directly and saving some complexity by not doing this check. But if you think users should check themselves, I can add. Though it would be a sort of surprising if your implementation would become the only C++ atomic implementation without this check.  
  
> As far as I remember, the original code used the same proxy for multiple atomics, which necessitated `notify_one` to wake up all threads instead of one. This is terribly inefficient.  
  
I'm not sure about being terribly inefficient. If there are far more table entries than CPU cores, it is not likely that there are far more waits than table entries, since user would not normally run too much more threads than cores.   
  
Sure it will be a problem if all threads wait on one atomic variable, but this is not very common pattern, usually there are distinct atomics.

---

## Comment 31

> Username: Lastique  
> Created at: 2020-06-10 07:54:57 UTC  
> Updated at: 2020-06-10 07:56:24 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641809701  

> Say I'm now using `WaitOnAddress` directly and saving some complexity by not doing this check.  
  
Then you are relying on (undocumented) behavior of that function. This is not portable.  
  
Again, based on opaque value of the atomic object, it is not possible to know if there are waiters. Doing so requires additional state, which requires a lock pool. I'm not going to use the lock pool unconditionally, as it defeats the purpose of using futex in the first place.  
  
> I'm not sure about being terribly inefficient. If there are far more table entries than CPU cores, it is not likely that there are far more waits than table entries, since user would not normally run too much more threads than cores.  
  
I think that the number of pending waits is generally not tied to the number of CPU cores. I also am quite sure that waking up a thread is expensive, especially when that wake up is useless and leads to blocking the thread again. More expensive than a single syscall, actually.

---

## Comment 32

> Username: AlexGuteniev  
> Created at: 2020-06-10 08:00:06 UTC  
> Updated at: 2020-06-10 08:01:42 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641813455  

> Again, based on opaque value of the atomic object, it is not possible to know if there are waiters.  
  
Wouldn't an atomic flag in wait table do? (Sure here we need fences for this atomic flag, even if `WaitOnAddress` / `futex` has its internal fence)  
  
Note that it is pool, but you don't need to lock mutexes on that pool.  
  
> I also am quite sure that waking up a thread is expensive, especially when that wake up is useless and leads to blocking the thread again. More expensive than a single syscall, actually.  
  
Good point.  
But if we actually wake no other threads in most of the cases, the optimization may be good.

---

## Comment 33

> Username: Lastique  
> Created at: 2020-06-10 08:05:33 UTC  
> Updated at: 2020-06-10 08:06:45 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641817315  

> Wouldn't an atomic flag in wait table do?  
  
No, because you need a counter, not a flag. Lock pool size is finite, so multiple atomic objects can map to a single counter, so it won't be that efficient either. And if you want to avoid locking the mutex, that counter needs to be updated atomically, which means `wait` becomes more expensive. Given that the user will likely have a way to do it more efficiently, based on the atomic object value, I don't think it is worth it.

---

## Comment 34

> Username: AlexGuteniev  
> Created at: 2020-06-10 08:11:46 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641820959  

Ok, I understood your rationale for not doing a "proxy futex" or kernel call saving.  
  
Fence that makes code in https://github.com/boostorg/atomic/issues/28#issuecomment-641697061 not to hang is still a question. I observe that `WakeByAddressAny` / `WakeByAddressAll` has this fence at entry, but it is not documented, and I don't know the situation about `futex`es.

---

## Comment 35

> Username: Lastique  
> Created at: 2020-06-10 08:21:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641828722  

I don't think the fence is needed. The atomic operations already provide the necessary guarantees and the notifying operation happens after the atomic store.

---

## Comment 36

> Username: AlexGuteniev  
> Created at: 2020-06-10 09:07:47 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641863075  

Assume a notifying operation is internally implemented as another store of a different variable, and this store is visible in `wait` thread, and has relaxed ordering.   
  
Then such notifying operation will behave correctly, if there's a fence after modification of notified variable.  
  
I don't think that such implementations are totally implausible.  
  
Since underlying APIs may have optimizations that you don't want to implement.  
  
I don't keep insisting on that you should do such optimization, I mean that they may be implemented in an underlying function, so fence is not a hypothetical concern, as API calls are not necessarily OS calls.   
  
Like atomic wait author's implementation has exactly flag, not counter, named `credit` for this:  
> No, because you need a counter, not a flag  
  
And Windows implementation has kernel call avoidance and indirect waits   
(Windows seem to use linked list in wait table entries, so wait table is not a disaster in terms of waking wrong threads, and as tables are populated by waiter, after it spins for some time, so it is not big impact for waiter either)

---

## Comment 37

> Username: AlexGuteniev  
> Created at: 2020-06-10 09:25:44 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641874470  

Maybe it worth adding C++20 `atomic_signed_lock_free` / `atomic_unsigned_lock_free` typedefs to tell users which atomic waits are real futexes?  
  
On Windows the answer is "any", but on Linux it is `int32_t` / `uint32_t`, so maybe the generic answer is  `int32_t` / `uint32_t`.  
  
Or is it expected that users know it by themselves, and just use `atomic<uint32_t>` ?

---

## Comment 38

> Username: Lastique  
> Created at: 2020-06-10 09:33:48 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641879916  

> Assume a notifying operation is internally implemented as another store of a different variable, and this store is visible in wait thread, and has relaxed ordering.  
>  
> Then such notifying operation will behave correctly, if there's a fence after modification of notified variable.  
  
A reasonable system blocking operation should include at least an acquire operation or fence internally, so that the unblocked thread observes the actual memory state. In practice, most systems have a lock-protected wait list internally.  
  
> Maybe it worth adding C++20 `atomic_signed_lock_free` / `atomic_unsigned_lock_free` typedefs to tell users which atomic waits are real futexes?  
  
These typedefs are an unfortunate consequence of (IMHO, misguided) attempt to define C interfaces for atomics in C++ standard. I'm contemplating deprecating them in Boost.Atomic.  
  
I'm working on IPC atomics now, and as part of that work will provide means to detect native support for waiting/notifying operations.

---

## Comment 39

> Username: Lastique  
> Created at: 2020-06-10 09:46:51 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641888064  

> > Maybe it worth adding C++20 `atomic_signed_lock_free` / `atomic_unsigned_lock_free` typedefs to tell users which atomic waits are real futexes?  
>   
> These typedefs are an unfortunate consequence of (IMHO, misguided) attempt to define C interfaces for atomics in C++ standard. I'm contemplating deprecating them in Boost.Atomic.  
  
I suppose, I could add these typedefs after all, at least so long as I haven't decided on the deprecation.

---

## Comment 40

> Username: AlexGuteniev  
> Created at: 2020-06-10 09:47:47 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-641888550  

> A reasonable system blocking operation should include at least an acquire operation or fence internally, so that the unblocked thread observes the actual memory state.  
  
Probably yes. But out of `wait` / `wake` pair, only `wait` is blocking.   
  
`wake` rather maps to `condition_variable::notify*`, which by itself does not provide fences (it is relying on the user using the same mutex as passed for `wait` operation).  
  
So I'm still a bit hesitating if fence in `notify` is implied by default.  
  
> I'm working on IPC atomics now, and as part of that work will provide means to detect native support for waiting/notifying operations.  
  
Got your point. Good luck in implementing IPC (I'm currently only interested in usual thread synchronization, but might come handy in the future).  
  
I would also like to see a C++way of detecting futex size, though I don't care much about existence of C interface non-deprecated in addition to that (except that `atomic<T>` constructor and `ATOMIC_FLAG_INIT` the attempt on supporting C did not hurt me).

---

## Comment 41

> Username: AlexGuteniev  
> Created at: 2020-07-19 16:21:37 UTC  
> Url: https://github.com/boostorg/atomic/issues/28#issuecomment-660671112  

> Generally, I see the `wait` without an argument as much more useful than either matching or mismatching one  
  
There's some thought in this direction. Atomic wait can take a predicate instead of undesired value. It would pass the observed value to that predicate, and if predicate evaluates to `false`, pass the value to underlying `futex`/`WaitOnAddress`.   
  
There's support for this from the proposal implementation author https://github.com/brycelelbach/wg21_p1135_cpp20_synchronization_library/issues/14#issuecomment-660657288
