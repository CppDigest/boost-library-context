# #1634 - Managed C++ (.NET) [Closed]

> Username: TopGun3  
> Created at: 2019-06-10 22:08:41 UTC  
> Updated at: 2019-08-09 05:47:08 UTC  
> Closed at: 2019-08-09 05:47:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1634  

Currently Beast has compilation errors when the Common Language Runtime Support (/clr) is enabled (Visual Studio 2019).  
  
Are there any plans to support managed c++?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-10 22:09:22 UTC  
> Updated at: 2019-06-10 22:09:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-500613258  

Does "Support Managed C++" require any code that is not standard C++?

---

## Comment 2

> Username: TechnikEmpire  
> Created at: 2019-07-02 00:27:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507472204  

Yes it does. /CLR enables c++/cli which is a Microsoft specific extension to the c++ language which creates .net assemblies.  
  
.net has a whole host of libraries that mirror this repository and more. HttpClient, Kestel and so on.

---

## Comment 3

> Username: TechnikEmpire  
> Created at: 2019-07-02 00:28:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507472386  

Its windows specific as well so its not even an option.

---

## Comment 4

> Username: TechnikEmpire  
> Created at: 2019-07-02 02:55:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507497760  

Also there's a ton of standard libraries which c++/cli will refuse to compile with so its not possible to even accommodate issues like this.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-07-02 15:50:12 UTC  
> Updated at: 2019-07-02 15:50:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507736268  

Beast is written in C++ so I don't think this is actionable. There are not really any plans to port it to C# or "managed C++."

---

## Comment 6

> Username: TopGun3  
> Created at: 2019-07-03 03:37:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507924659  

Sorry for the late reply.  
  
From my preliminary investigation there appears to only be two issues to get Beast compiling in managed C++.  
  
As a side note,  a lot of managed c++ does compile native c++ without too much problem.  Whilst there are many workarounds to interop managed code with native code it is nice to be able to compile in, and call, managed code directly.  
  
The first issue with Beast appears trivial to remedy, namely rather than #including mutex a minor code change would be required to detect the compiler and #ifdef #include boost/thread/mutex.hpp instead and make the appropriate code changes.  If I can get fix the second issue (below) I will propose a patch for your consideration.  
  
The second issue could probably be trivial to remedy but it involves templated code and it is difficult for me to  identify precisely where the issue is.  The compiler error appears to be trying to call a copy constructor (which has been deleted) instead of the intended move constructor.  
  
If I comment about 5 lines of code in the Beast library code then the example websocket server does compile in managed c++ (but, for obvious reasons, will not work).  The lines I have commented out are:  
  
`transfer_op<false, Buffers, typename std::decay<WriteHandler>::type>(std::forward<WriteHandler>(h), *s, b);`  
  
`write_op<typename std::decay<WriteHandler>::type, Stream, Predicate, isRequest, Body, Fields>(std::forward<WriteHandler>(h), *s, *sr);`  
  
`idle_ping_op<Executor>(sp, get_executor());`  
  
`response_op<typename std::decay<AcceptHandler>::type>(std::forward<AcceptHandler>(h), sp, *m, d);`  
  
`read_some_op<read_op, mutable_buffers_type>(std::move(*this), sp, *mb);`  
  
There could be more issues I am unaware of once those templates have been instantiated.  
  
When I get some free time I'll extract out the templated code from the Beast library into something more understandable and see if I can identify precisely why the managed c++ compiler is trying to call a deleted copy constructor instead of a move constructor.

---

## Comment 7

> Username: TechnikEmpire  
> Created at: 2019-07-03 03:43:44 UTC  
> Updated at: 2019-07-03 03:48:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507925781  

I'm trying to tell you as someone with years of experience this isn't going to be easy to support if its possible at all and I'm still unclear as to why its a goal at all. Use HttpClient. If you need websockets use their managed websockets library (look up pingman tools). If you need a full blown web server use Kestrel.  
  
BTW I'm not a maintainer or affiliated with the project I'm just an observer. C++/CLI will fight against most features introduced at c++11 and later.  
  
Furthermore you do realize that /CLR is a per-file option right?  
  
Disable it explicitly in the project, add a new class and hide the implementation that calls beast with PIMPL and enable /clr on ONLY that file. Problem solved you'll get a mixed managed assembly and there's absolutely no reason to mess with beasts code at all.

---

## Comment 8

> Username: TechnikEmpire  
> Created at: 2019-07-03 03:46:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507926213  

If you still have issues do pragma unmanaged/pragma managed with your beast includes in between and modify your interface until your compiler issues go away. The CLR will automatically generate pure unmanaged and managed outputs into the same DLL and generate hidden pinvoke calls to bridge them.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-07-03 04:03:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507929199  

The bigger problem is that "Managed C++" is not a tested configuration in any of the CI builds, it is not something that I maintain locally, and even if it was on CI or in a local configuration, I have no knowledge of what to do if it breaks. If I was to apply a patch today, there is no guarantee it would work in the next Boost release. And in fact Beast would most likely break as I continue to make changes, unaware of the special needs of "Managed C++."

---

## Comment 10

> Username: TopGun3  
> Created at: 2019-07-03 04:07:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-507929739  

Thanks for the comments.  It would appear the solution proposed by TechnikEmpire appears good in which case this issue could be closed.

---

## Comment 11

> Username: stale[bot]  
> Created at: 2019-08-02 04:52:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-517547502  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 12

> Username: stale[bot]  
> Created at: 2019-08-09 05:47:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1634#issuecomment-519787023  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
