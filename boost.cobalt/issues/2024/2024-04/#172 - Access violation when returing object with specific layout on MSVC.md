# #172 - Access violation when returing object with specific layout on MSVC [Open]

> Username: Lyulf  
> Created at: 2024-04-17 14:22:31 UTC  
> Updated at: 2025-08-11 10:33:00 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172  

Code:  
```c++  
#include <cstdint>  
#include <cstdio>  
#include <string>  
  
#include <boost/cobalt.hpp>  
  
namespace cobalt = boost::cobalt;  
  
struct ExampleStruct  
{  
  std::string str;  
  std::uint8_t padding[32];  
};  
  
cobalt::task<ExampleStruct> getResult()  
{  
  // error on this return  
  co_return {};  
}  
  
cobalt::main co_main(int /*argc*/, char** /*argv*/)  
{  
  co_await getResult();  
  // Never displayed  
  std::puts("Done");  
  co_return 0;  
}  
  
```  
  
Built on Windows 10 64-bit using MSVC 17.9.5 (cl version 19.39.33523 for x64) with following command:  
```  
cl /Femain.exe main.cpp /nologo /external:IC:\Users\chodorowskip\repo\deps\vc143\boost-1.85.0\include\boost-1_85 /std:c++20 /MD /Z7 /O2 /EHsc /D_WIN32_WINNT=0x0601 /W4 /external:W0 /link /PROFILE /LIBPATH:C:\Users\chodorowskip\repo\deps\vc143\boost-1.85.0\lib libboost_cobalt-vc143-mt-x64-1_85.lib  
```  
  
Error  
```  
Exception thrown at 0x00007FF728E265E1 in main.exe: 0xC0000005: Access violation reading location 0xFFFFFFFFFFFFFFFF.  
```  
  
Error doesn't seem to happen with debug configuration.

---

## Comment 1

> Username: Pele44  
> Created at: 2024-04-18 07:46:12 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2063236695  

I've check this code on every combination of g++ 13.2 /clang++ 17.0.6, -O2 / -O0, no sanitizer / address sanitizer / thread sanitizer on Linux platform without any problems. In every case "Done" is printed and no sanitizer warning are reported.  
But on MSVC crash occurs (Windows 11), I can confirm it.

---

## Comment 2

> Username: Pele44  
> Created at: 2024-04-26 07:15:31 UTC  
> Updated at: 2024-04-26 07:17:12 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2078774936  

Is there a chance that the error is actually in cobalt? Or maybe it's better to report the case to Microsoft? The problem occurs in our project and unfortunately the current workaround is to stop using cobalt, which we would really like not to do, because it is supposed to be a very helpful library, making it easier to solve many of the problems we face. And the specificity of the project means that the code must run on packages generated using MSVC and gcc... We cannot give up the Microsoft compiler.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-04-26 15:38:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2079633567  

I had plenty of problems with MSVC and last time I checked they had issues with improper inlining (for which I added a workaround). I however don't like to say "it's their fault" as it might be mine. I didn't get around to checking the code yet - which is why I didn't respond, sorry.

---

## Comment 4

> Username: Pele44  
> Created at: 2024-04-26 16:52:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2079747486  

Thanks for the answer!

---

## Comment 5

> Username: fgoujeon  
> Created at: 2024-04-27 10:56:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2080451416  

It this related to this [MSVC bug](https://developercommunity.visualstudio.com/t/coroutine-Access-violation-reading-locat/1383447?q=+Cannot+read+properties+of+undefined+%28reading+%27coreFields%27%29)?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2024-04-28 02:53:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2081302319  

It might be. I added commit increasing the align [here](https://github.com/boostorg/cobalt/tree/max_align).   
  
I don't know when I get my windows builds working again, so if you get around to test it let me know.

---

## Comment 7

> Username: Lyulf  
> Created at: 2024-04-30 09:46:45 UTC  
> Updated at: 2024-04-30 12:35:38 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2084854811  

I couldn't figure out how to build cobalt without bootstrap and b2, so I just applied the changes from the most recent commit from the branch you provided and they don't seem to work. Still got access violation.

---

## Comment 8

> Username: Pele44  
> Created at: 2024-07-12 04:50:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2224608441  

Is there a chance that the mentioned bug/workaround for the MSVC bug will be fixed in the near future?

---

## Comment 9

> Username: Mishura4  
> Created at: 2025-01-24 19:28:45 UTC  
> Updated at: 2025-01-24 19:39:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2613237788  

Spent a lot of time converting a lot of our async callbacks to coroutines, only to find out this happens in release.  
  
Any way this can be looked at?  
  
EDIT: For anyone with this issue, the `BOOST_COBALT_NO_PMR` preprocessor definition saves the day.

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2025-01-26 13:46:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2614431506  

Which boost version did you build with?

---

## Comment 11

> Username: Mishura4  
> Created at: 2025-01-27 17:41:59 UTC  
> Updated at: 2025-01-27 17:42:37 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-2616485196  

Boost is 1.87, MSVC is latest non-preview version (14.42.34433)

---

## Comment 12

> Username: konradglas  
> Created at: 2025-08-11 06:49:53 UTC  
> Updated at: 2025-08-11 06:50:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-3173460723  

@klemens-morgenstern I tested [this branch ](https://github.com/boostorg/cobalt/tree/max_align)  
It still crashes for me at the exact same place. It definitely is the msvc compiler bug that assumes 16 byte aligment (according to the post) . What works for me is to over-align the promise_type itself like so:  
```  
struct alignas(32) task_promise : promise_memory_resource_base,  
                                  promise_cancellation_base<asio::cancellation_slot, asio::enable_total_cancellation>,  
                                  promise_throw_if_cancelled_base,  
                                  enable_awaitables<task_promise<Return>>,  
                                  enable_await_allocator<task_promise<Return>>,  
                                  enable_await_executor<task_promise<Return>>,  
                                  enable_await_deferred,  
                                  task_promise_result<Return>  
{  
```  
Note that `alignas(16)` doesn't seem to cut it.

---

## Comment 13

> Username: klemens-morgenstern  
> Created at: 2025-08-11 07:46:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-3173613684  

Thank you for that. I hopefully find the time to look into that, here's   
  
If seems this has been fixed in a recent MSVC release:  
https://developercommunity.visualstudio.com/t/C20-coroutine-variables-are-not-proper/10049371  
  
I am not sure how to work around this - probably a version check (if the fix works) and then do a 32 align.  
  
I just wish they fixed this one:  
https://developercommunity.visualstudio.com/t/Unable-to-destroy-C20-coroutine-in-fin/10657377

---

## Comment 14

> Username: konradglas  
> Created at: 2025-08-11 08:22:51 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-3173726727  

yeah ... weird and sad that they are not fixing this. I have tried with the latest msvc (17.14) and it still crashes. So no :( they didn't fix that particular part. FWIW this would be a fix that compiles for msvc, latest apple-clang and GCC (Ubuntu)  
```  
#ifdef _MSC_VER  
#define OVERALIGN alignas(32)  
#else  
#define OVERALIGN  
#endif  
  
struct async_initiate_spawn;  
  
template <typename Return>  
struct OVERALIGN task_promise : promise_memory_resource_base,  
```  
regarding this bug [https://developercommunity.visualstudio.com/t/Unable-to-destroy-C20-coroutine-in-fin/10657377](https://developercommunity.visualstudio.com/t/Unable-to-destroy-C20-coroutine-in-fin/10657377)  
before I run into an msvc coro quirk again. Does this have any implications when running cobalt? Should I avoid something in particular?

---

## Comment 15

> Username: klemens-morgenstern  
> Created at: 2025-08-11 10:05:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-3174049590  

I'll need to add a `BOOST_COBALT_MSVC_ALIGN_PROMISE` I think, and then users can override it. the 32 is for the 64 bit compiler? I reckon 32 byte might work for the 32 bit then.  
  
The other bug is worked around, the self delete does a post to the event loop. I.e. this is slower than on clang/gcc, but works. The general issues msvc has is bad inlining. I.e. it can inline function calls into the coroutine frame which can lead to horrible bugs. I am using a bunch of `noinline` in many places in the code, but I might have missed something - it's hard to test for compiler errors.

---

## Comment 16

> Username: konradglas  
> Created at: 2025-08-11 10:33:00 UTC  
> Url: https://github.com/boostorg/cobalt/issues/172#issuecomment-3174141923  

Yes I was fearing that as well.   
I am also very strict for that reason regarding inlining on msvc.  
  
This is for the 64 bit compiler. I cannot check but I thought this bug only occurs for 64 bit.
