# #90 add initial cmake script (for compiling only boost_stacktrace_windbg) [Closed]

> Username: tapika  
> Created at: 2020-04-18 16:08:46 UTC  
> Updated at: 2024-06-21 15:02:01 UTC  
> Closed at: 2024-06-21 13:55:27 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90  

Added initial cmake, for now just builds existing library.  
I will try to add c++/clr support and C# support later on, that's why want to go with cmake.  
  
This is initial pull request, will try to get more pulls next.

---

## Comment 1

> Username: coveralls  
> Created_at: 2020-04-19 11:41:13 UTC  
> Updated_at: 2020-05-01 03:41:46 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-616112156  

[![Coverage Status](https://coveralls.io/builds/30481851/badge)](https://coveralls.io/builds/30481851)  
  
Coverage decreased (-3.2%) to 89.079% when pulling **6942b92477679d97a865ed62e3dbb8b52be8f0c0 on tapika:develop** into **f931528c87f19757e060f5fee1a4f8ccdbacf7a0 on boostorg:develop**.

---

## Review 2 [Changes requested]

> Username: apolukhin  
> Created_at: 2020-05-02 05:13:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/stacktrace/pull/90#pullrequestreview-404501444  

* don't put different changes into a single PR. One bad change causes the whole PR to be rejected  
* remove all the cmake related commits, Boost libraries use different approach for cmake files. Here's an example https://github.com/boostorg/conversion/blob/develop/CMakeLists.txt  
* do not embed third party libraries into Boost. Users have to install them by their own  
* do not silently change the user provided options. For example, users may have reasons to not cache COM related stuff

---

## Comment 3

> Username: tapika  
> Created_at: 2020-05-02 06:39:47 UTC  
> Updated_at: 2020-05-02 06:42:18 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-622731459  

> * don't put different changes into a single PR. One bad change causes the whole PR to be rejected  
  
My first intent was to try out whether cmake is acceptable or not, but since long no-accept delay, I've started to work on complete feature, resulting in everything being pushed in, which is not good, but that would be eventual target, I guess.  
  
> * remove all the cmake related commits, Boost libraries use different approach for cmake files. Here's an example https://github.com/boostorg/conversion/blob/develop/CMakeLists.txt  
  
Yes, I have noticed that indeed. cmake support in boost is incomplete still, requires overall cmake enabling on boost level. I guess cmake port to boost was abandoned once upon a time, and it's left in early beta stage.  
  
When started to add cmake support, I've noticed that one - and my idea was to eventually make cmake support so that stacktrace could have "standalone" cmake support. "standalone" basically would mean that you could use cmake on stacktrace project level without pulling boost or other boost libraries to your hard disc.   
  
That is not fully true yet, as cpp_crashy_app (former terminate_handler) is dragging boost_date_time & boost_filesystem dependencies. But I plan to work on this later on to cut off or simplify cmake dependencies.  
  
My idea is that to work on stacktrace , you need boost headers + precompiled boost libs + stacktrace git.  
  
At the moment I'm using cmake from boost root level with test applications disabled, e.g. command line like this:  
  
    cmake -DBOOST_EXCLUDE_LIBRARIES=atomic;timer;regex -DBUILD_TESTING=OFF -DCMAKE_GENERATOR_PLATFORM=x64 -Bproj_vs2019 -S.  
  
> * do not embed third party libraries into Boost. Users have to install them by their own  
  
Unfortunately it's not possible to have good exception handler for native+managed code without minhook. At the moment I'm planning to have stacktrace static library, which will embed minhook source code. For native code it's possible to use 1. AddVectoredExceptionHandler & 2. SetUnhandledExceptionFilter - both pure windows api. But to be able to debug for example 2 - minhook library would still be needed, as it has something to do with visual studio using exception handling mechanism together with debugger. (I have now integrated support native + managed exception handling, planning to check native exception handling later on). If end-user will use stacktrace library, he will not need to install anything separately on his own.  
  
> * do not silently change the user provided options. For example, users may have reasons to not cache COM related stuff  
  
This is something I have discovered by myself that it's possible to use faster call stack resolution, where com is enabled only once, and used for eternity - was wondering why it just could not be automatically enabled by default. It's possible to query from used defines, whether functionality can be activated and used. Is the problem that query could be executed from multiple threads, and all of them needs to be com enabled eventually ?!  
  
I overall, I hope to get some of your help, and this is first time I'm making modifications to boost libraries itself, and not fully familiar with code structure or design rules.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2020-05-05 18:36:11 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-624232720  

If you do not address the issues, the PR would not be accepted.  
  
Copy-pasting third party libraries is a very bad idea. Boost tries to design a common CMake approach and you need to talk to people who does that to avoid common pitfalls. Separate PR into pieces.  
  
Not caching COM has reasons. Some users may print traces before initting COM, so caching COM on Boost side may break user code: https://github.com/boostorg/stacktrace/blob/293e1f43f697ecad70582724b96b868c8ebe2162/include/boost/stacktrace/detail/frame_msvc.ipp#L54-L61

---

## Comment 5

> Username: tapika  
> Created_at: 2020-05-05 21:31:27 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-624317503  

> If you do not address the issues, the PR would not be accepted.  
  
Ok, let's take one issue at the time, I need to resort all problems in here.  
  
> Copy-pasting third party libraries is a very bad idea. Boost tries to design a common CMake approach and you need to talk to people who does that to avoid common pitfalls. Separate PR into pieces.  
  
Do you know with whom to discuss about cmake changes ?  
  
> Separate PR into pieces.  
  
At the moment whole my attempt is more or less alpha version of managed call stack resolving, I haven't yet addressed .net framework side fully, and .net core cannot be addressed. I hope eventually that Microsoft guys will take over for this library to become a standard, because without them whole system is very fragile and more or less falling apart.   
  
Need to indeed split PR into pieces.  
  
Does it makes any sense if I'll cancel this PR, and check later on in how many PR's I can push it through ?  
  
> Not caching COM has reasons. Some users may print traces before initting COM, so caching COM on Boost side may break user code:  
>   
> https://github.com/boostorg/stacktrace/blob/293e1f43f697ecad70582724b96b868c8ebe2162/include/boost/stacktrace/detail/frame_msvc.ipp#L54-L61  
  
not caching also results in slower performance, this especially can be seen with my own c++/clr changes - managed call stack query indeed takes ages. But not all frames can be resolved using native symbol information, need to integrate managed symbol information into this system as well, see:  
  
https://sourceforge.net/p/diagnostic/svn/HEAD/tree/src/ResolveStackM.cpp  
  
Btw, my own original implementation for native call stack resolving was not using COM at all.  
  
https://sourceforge.net/p/diagnostic/svn/HEAD/tree/src/ResolveStack.cpp  
  
But managed symbol resolving will require COM indeed.  
  
I'm also not sure what is the intention with Microsoft .net core. Microsoft guys will need indeed call stack resolving for C++ as well, is their intention to port C++ compiler to linux, port COM to linux, or what will be the target.  
  
This is requirement I have raised for them:  
  
https://github.com/dotnet/runtime/issues/12405  
  
but I think your approach is better - try to this as a C++ standard. Should be that one long time ago.

---

## Comment 6

> Username: tapika  
> Created_at: 2020-05-08 08:38:04 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-625708134  

@Fraser999 I've checked your answer on stack overflow:  
https://stackoverflow.com/questions/18354398/is-it-possible-to-build-boost-with-cmake  
  
Concerning building of boost using cmake. Do I understand correctly that boost cmake is pretty much abandoned project by now ? I guess main problem is that boost build uses same mechanisms as cmake to specify include directories, files, etc, as boost build. If we have cmake and boost build in parallel - then need to maintain two build systems by all teams. This is pretty much impossible / difficult target to achieve.  
  
I by myself started to add cmake to stacktrace, but I've only scratched the surface of current stacktrace's Jamfile.v2, and only added example projects - but need also to re-write same things from boost build to cmake and vise versa => difficult if not impossible.  
  
The main reason why I've started to use cmake for stacktrace - is because cmake is capable of generating c++/clr + c# projects, feature, which is not available in boost build.  
  
I guess if we speak in long term perspective - maintaining two build system is indeed a nightmare.  
There could be couple of alternatives how to tackle two concurrent build system, namely:  
1. cmake files can be generated from boost build jam files,   
or   
2. boost build jam files are generated from cmake files.   
  
Both cmake & boost build language dialects is bit difficult to learn, after which it's also possible to create completely new 3rd alterantive, which is not based on both build systems.  
  
E.g. c++ scriptable + project / solution modelling, I've started scratching something, but wiring everything together to make system fully functional can require some effort.   
(https://github.com/tapika/cppscriptcore)  
  
Would it be ok for now if stacktrace cmake would be completely independent from boost cmake - at least I want that you can build stacktrace independently from boost and rest of boost libraries.  
  
Such build isolation could be also good target to achieve also for build boost build system, as what I have tried now by myself - it's not possible to build only specific boost library without dragging rest of boost libraries.  
  
This somehow coechoes with my recently posted question / answer on stack overflow:  
https://stackoverflow.com/a/61674071/2338477  
  
But boost has different requirements (e.g. linux, mac, ...) and that answer might not fully answer boost needs. Actually wondering if someone would / could start to code scripts to manage external repositories allowing mechanism of switching between source code and prebuilt libraries.  
Maybe such does exists for boost, but I'm not aware of that one only ?!

---

## Comment 7

> Username: Fraser999  
> Created_at: 2020-05-12 10:32:13 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-627257729  

> Do I understand correctly that boost cmake is pretty much abandoned project by now ?  
  
I'm not sure if this question is directed at me, but I'm afraid I don't know.  I didn't have anything to do with that project, and have exclusively been using Rust since late 2015, so I haven't kept up to date with CMake or Boost.

---

## Comment 8

> Username: leha-bot  
> Created_at: 2021-08-10 12:00:02 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-895968928  

@tapika  hello, do you plan to continue work on CMake? I could help with it, if you want to continue and don't mind

---

## Comment 9

> Username: tapika  
> Created_at: 2021-08-10 20:15:42 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-896289257  

Hi !  
I think I've pretty much cross compiled boost::stacktrace with cmake at the moment.There are some downsides (concerning stacktrace functionality itself) and opportunities.  
Why do you ask ?  
  
-- Have a nice day!     Tarmo.   
  
    On Tuesday, August 10, 2021, 03:00:15 PM GMT+3, Alex ***@***.***> wrote:    
   
   
  
  
@tapika hello, do you plan to continue work on CMake? I could help with it, if you want to continue and don't mind  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub, or unsubscribe.

---

## Comment 10

> Username: pdimov  
> Created_at: 2024-06-21 13:55:27 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-2182804458  

This doesn't seem relevant anymore.

---

## Comment 11

> Username: tapika  
> Created_at: 2024-06-21 15:02:00 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/90#issuecomment-2182923052  

My own fork besides cmake support also implements stacktrace capability for managed c++ (c++/cli). But solution is not final - resolving managed stack frames requires much more implementation.   
  
As stacktrace now goes into c++ standard, I hope that Microsoft will somehow implement that managed c++ support and we can just use it out of box.

---
