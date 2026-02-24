# #291 - Compilation with Mingw-w64 (g++ 5.2.0 and 9.2.0) fails (Boost 1.71.0) [Open]

> Username: Bihlerben  
> Created at: 2019-09-16 10:28:48 UTC  
> Updated at: 2019-09-18 07:48:39 UTC  
> Url: https://github.com/boostorg/thread/issues/291  

When trying to compile the example code from this question: https://stackoverflow.com/questions/49453476/using-boost-thread-pool-executor-submit-method  
compilation fails and this error message is given by both g++ 5.2.0 and g++ 9.2.0 (as part of Mingw-w64):  
  
mingw32-make -j20 all   
g++ -c -pipe -isystem C:\Libraries\OpenCascade\oce\InstallMingw64\include\oce -isystem C:\Libraries\boost_1_71_0 -std=gnu++1y -Wpedantic -ffunction-sections -fdata-sections -mtune=generic -O2 -DNDEBUG -frtti -fexceptions -mthreads -Wall -Wextra -DUNICODE -DQT_DLL -DQT_NO_DEBUG -DQT_GUI_LIB -DQT_CORE_LIB -DQT_HAVE_MMX -DQT_HAVE_SSE -DQT_HAVE_MMXEXT -DQT_HAVE_SSE2 -DQT_THREAD_SUPPORT -DQT_NEEDS_QMAIN -I [... here come the include directories] -o Main.o ..\src\Main.cpp  
In file included from C:\Libraries\boost_1_71_0/boost/thread/detail/thread.hpp:26:0,  
                 from C:\Libraries\boost_1_71_0/boost/thread/thread_only.hpp:22,  
                 from C:\Libraries\boost_1_71_0/boost/thread/thread.hpp:12,  
                 from C:\Libraries\boost_1_71_0/boost/thread.hpp:13,  
                 from ..\src\Main.cpp:7:  
C:\Libraries\boost_1_71_0/boost/thread/detail/invoke.hpp: In function 'decltype (*(forward<A0>)(*boost::detail::invoke::a0).**boost::detail::invoke::f) boost::detail::invoke(Fp&&, A0&&) [with Fp = void (boost::executors::basic_thread_pool::*)(); A0 = boost::executors::basic_thread_pool*; decltype (*(forward<A0>)(*boost::detail::invoke::a0).**boost::detail::invoke::f) = void (boost::executors::basic_thread_pool::)()]':  
C:\Libraries\boost_1_71_0/boost/thread/detail/invoke.hpp:102:43: internal compiler error: in gimplify_expr, at gimplify.c:8786  
         return (*boost::forward<A0>(a0)).*f;  
                                           ^  
libbacktrace could not find executable to open  
Please submit a full bug report,  
with preprocessed source if appropriate.  
See <http://sourceforge.net/projects/mingw-w64> for instructions.  
mingw32-make: *** [Main.o] Error 1  
Makefile:187: recipe for target 'Main.o' failed  
"mingw32-make -j20 all" terminated with exit code 2. Build might be incomplete.

---

## Comment 1

> Username: Bihlerben  
> Created at: 2019-09-16 12:34:40 UTC  
> Url: https://github.com/boostorg/thread/issues/291#issuecomment-531757383  

Oh, did I make a mistake and this is actually a gcc bug? Would you still have a look at it and see whether there is a workaround?

---

## Comment 2

> Username: Bihlerben  
> Created at: 2019-09-16 12:36:32 UTC  
> Url: https://github.com/boostorg/thread/issues/291#issuecomment-531758012  

By the way, "#define BOOST_THREAD_PROVIDES_EXECUTORS" seems to be the culprit that triggers the error.

---

## Comment 3

> Username: Bihlerben  
> Created at: 2019-09-17 12:56:57 UTC  
> Url: https://github.com/boostorg/thread/issues/291#issuecomment-532208390  

One more remark: I can solve the compilation issues by using #define BOOST_NO_SFINAE_EXPR.

---

## Comment 4

> Username: viboes  
> Created at: 2019-09-17 21:19:55 UTC  
> Url: https://github.com/boostorg/thread/issues/291#issuecomment-532406615  

If you don't want to use executors you can not define BOOST_THREAD_PROVIDES_EXECUTORS, but then the example has less sense.  
  
BOOST_NO_SFINAE_EXPR is defined or not by Boost.Config. If this i sthe thing to change, you should provide a patch to Boost.Config so that it is defined according to the compiler version.  
  
Anyway, the invoke function in C++98 is quite fragile, and maybe need adaptation to new compiler version or platforms the Boost.Thread library was not aware of, when the code was created.  
  
Boost.Thread don't work well with Mingw :(

---

## Comment 5

> Username: Bihlerben  
> Created at: 2019-09-18 07:48:39 UTC  
> Url: https://github.com/boostorg/thread/issues/291#issuecomment-532564509  

Hmmm, I was trying to use boost::async and boost::future as a thread-pool-ready replacement for std::async and std::future with Mingw. Perhaps it is not yet the right time to do that...?  
  
I have posted a question regarding my current problem here: https://stackoverflow.com/questions/57987570/why-do-iterative-calls-to-boostasync-get-stuck-when-using-a-thread-pool.  
  
Your code looks promising and is thrilling. I hope that you proceed and that I eventually get it to work with Mingw.
