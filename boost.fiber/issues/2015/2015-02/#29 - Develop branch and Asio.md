# #29 - Develop branch and Asio? [Closed]

> Username: erakadjiev  
> Created at: 2015-02-11 12:36:44 UTC  
> Updated at: 2015-02-20 01:08:35 UTC  
> Closed at: 2015-02-19 07:26:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/29  

Hello Oliver,  
  
I contacted you a week ago to ask which development branch to use. I saw that in the meantime `develop` became the one.   
  
I just got around to starting to work with Boost Fiber. I built it with modular Boost (which btw. wasn't trivial) and wanted to re-read the Asio examples and start coding. I have to use Fibers together with Asio. Unfortunately, I saw that yesterday you have first modified and then deleted the Asio-related examples. Doesn't the integration with Asio work anymore? If so, then what do you recommend? Can the Asio-related problems be fixed easily? Or should I use an older commit or the old master branch?  
  
Thank you very much!  
  
Best regards,  
Emil  
  
PS: my use of Fibers would be simple: One main fiber that creates other fibers for doing some work. This work includes asynchronous I/O through Asio, for which the fiber should yield. I'd also like to have a simple scheduler. All fibers would run on the same thread.

---

## Comment 1

> Username: olk  
> Created at: 2015-02-12 07:17:29 UTC  
> Updated at: 2015-02-12 07:24:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74027811  

2015-02-11 13:36 GMT+01:00 erakadjiev notifications@github.com:  
  
> Hello Oliver,  
>   
> I contacted you a week ago to ask which development branch to use. I saw  
> that in the meantime develop became the one.  
>   
> I just got around to starting to work with Boost Fiber. I built it with  
> modular Boost (which btw. wasn't trivial) and wanted to re-read the Asio  
> examples and start coding. I have to use Fibers together with Asio.  
> Unfortunately, I saw that yesterday you have first modified and then  
> deleted the Asio-related examples. Doesn't the integration with Asio work  
> anymore? If so, then what do you recommend? Can the Asio-related problems  
> be fixed easily? Or should I use an older commit or the old master branch?  
>   
> Thank you very much!  
>   
> Best regards,  
> Emil  
>   
> the problem is that boost.fiber is C++14-only and some APIs are  
> incompatible - for instance boost.fiber uses std::exception_ptr and  
> boost.asio requires boost::exception_ptr

---

## Comment 2

> Username: olk  
> Created at: 2015-02-12 09:05:18 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74038453  

I'll commit a new version of asio examples soon - be aware that boost.fiber requires C++14

---

## Comment 3

> Username: erakadjiev  
> Created at: 2015-02-12 12:51:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74065565  

Thank you for the reply and the information!  
The C++14 requirement would likely be an issue for me.   
For now, I started using the master branch and I have a question regarding that: do the Asio-related things work there under C++11? I tried and got compilation errors, but maybe because I compiled the rest of Boost with C++03.

---

## Comment 4

> Username: erakadjiev  
> Created at: 2015-02-17 05:48:56 UTC  
> Updated at: 2015-02-17 07:32:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74620196  

I just spent some time to try and fix the C++11 build issues in the `master` branch.   
  
One problem was that `forward` is used without a namespace specifier and this causes conflicts in C++11. If all occurences are simply replaced with `boost::forward`, then this issue is solved.  
  
The other problem is related to `chrono`. It requires a bit more work, because Boost Fiber interacts with Boost Thread and Boost Asio using chrono `time_point`s, and there is a discrepancy between which type of `chrono` those libraries use. Boost Asio automatically switches to `std::chrono` if it detects a C++11 compiler flag, but Boost Thread has not been updated to do this, and also the master branch of Boost Fiber doesn't have support for `std::chrono`.   
One solution is to recompile Boost Asio with BOOST_ASIO_DISABLE_STD_CHRONO.   
The other one is to modify `examples/asio/loop.hpp`. In the `timer_handler` function, the return value of `boost::fibers::detail::scheduler::instance()->next_wakeup()` has to be converted from `boost::chrono::time_point` to `std::chrono::time_point` before passing it as a parameter to Asio's `timer.expires_at()`. I used a conversion based on `to_time_t` and `from_time_t` methods. Furthermore, in the `run_service` function, `boost::chrono::seconds(0)` has to be replaced with `std::chrono::seconds(0)`, and the `<boost/chrono/system_clocks.hpp>` include has to be replaced with `<chrono>`.   
For a nicer, more universal solution, conditional compilation using the `BOOST_ASIO_HAS_STD_CHRONO` macro should be used.  
  
With those changes, the library compiles and the tests run successfully, but when I try to use Fibers in my code, I get a segfault (the coroutine call `( * f->callee_)( caller_, null_ptr)` at the end of the `resume` method of `fiber/detail/worker_fiber.hpp` causes it).

---

## Comment 5

> Username: olk  
> Created at: 2015-02-17 07:29:24 UTC  
> Updated at: 2015-02-17 07:29:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74627518  

Well, the code in master is 'deprecated' :/ I'm working on branch develop which will be merged into branch master soon. The issue related to boost.asio is already solved in branch develop - but unfortunately it requires some C++14 features.

---

## Comment 6

> Username: erakadjiev  
> Created at: 2015-02-17 07:39:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74628353  

It'd be the best if I could use the `develop` branch, but in my use-case I'm already running into issues due to C++03 and C++11 incompatibility, and I suppose that if I mix C++14 in, even more problems will pop up. That's why I'm trying to stick to the old `master` branch.  
  
PS: I updated my previous comment, because when using the modified library in my code, I get a segfault.

---

## Comment 7

> Username: erakadjiev  
> Created at: 2015-02-17 11:01:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74650932  

Regarding the segfault: previously, I did the C++11 Fibers tests both with the current newest version of Modular Boost and the stable 1.57.0 version, and both with `BOOST_ASIO_HAS_STD_CHRONO` defined and not defined. I always got the segfault.  
  
Now, I tried the `develop` branch with C++14, but again, I get a segfault.  I noticed that the `join()` on the fiber that calls `boost::fibers::asio::run_service` returns almost immediately.  
  
This implies that the problem is in my code, but I haven't identified yet what it is. It's nonetheless strange, that the code works perfectly when compiled with `g++ myclass.cpp [...]`, but if I use `-std=c++11` or `-std=c++14`, it segfaults. I'll keep looking for the problem.

---

## Comment 8

> Username: erakadjiev  
> Created at: 2015-02-18 04:40:11 UTC  
> Updated at: 2015-02-18 09:13:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74810962  

With C++14 I managed to solve the segfault issue (but there is a different issue now).  
  
I was `spawn`ing a main fiber that in turn `spawn`ed many other child fibers, and only some of those children were doing async Asio operations. As in the examples, after `spawn`ing the main fiber, I ran a fiber that called `boost::fibers::asio::run_service` (`io_service.run()`) and `join()`ed it. When processing was done, I called `io_service.stop()` from one of the child fibers.  
  
With C++03 (`master`, Boost 1.57) this worked correctly.  
With C++11 (`master`, Boost 1.57) and C++14 (`develop`, Boost 1.58) it led to a segfault, because the `join()` on the io_service runner fiber returned almost immediately and the subsequent cleanup code was executed prematurely.  
  
I replaced the `spawn`ing of fibers that don't do Asio operations (most importantly that of the main fiber) with normal fiber creation. This way, in C++14 the processing completed successfully without segfault.   
  
However, my program doesn't terminate now. The `join()` on the io_service running fiber doesn't return, even though, `io_service.stop()` has been called and the internal timer used by the fiber is not active anymore.  
Also, the C++11 version still segfaults.

---

## Comment 9

> Username: erakadjiev  
> Created at: 2015-02-18 10:51:28 UTC  
> Updated at: 2015-02-18 11:17:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74845602  

I was getting nowhere with the C++/Boost/Fiber version problem debugging, because I didn't know if my code is wrong or the libraries, so I decided to build and run the included Asio examples (I should have done this earlier).   
  
Here are the results:  
  
Compilation was successful in all cases. I directly used g++ (not Boost Build) version 4.9.2 on 64-bit Ubuntu 14.04.1.   
I used no/`-std=c++11`/`-std=c++14` flag, for C++03/C++11/C++14 respectively.  
To switch between the various Boost distributions, I used symlinks as `/usr/local/include/boost` for the headers, and specified the location of the respective library files on the command line (-L/... and -Wl,-rpath=/...).  
  
| Test | Branch | C++/Boost ver. | Result |  
| --- | --- | --- | --- |  
| **echo_server / echo_client** | `develop` | C++14/ModBoost 1.58 | **Fails.** The server starts, but never goes past "wait for accept". The client gets until "Enter message:" but nothing happens when I enter something. |  
|  | `master` (incl. my C++11 fixes) | C++11/Boost 1.57 | **Works.** |  
|  | `master` | C++03/Boost 1.57 | **Works.** |  
  
| Test | Branch | C++/Boost ver. | Result |  
| --- | --- | --- | --- |  
| **publish / subscribe** | `develop` | C++14/ModBoost 1.58 | **Fails.** I can start server, publisher and subscriber, but nothing happens when pubs/subs connect or publish something. |  
|  | `master` (incl. my C++11 fixes) | C++11/Boost 1.57 | **Fails.** Server segfaults as soon as I start it. |  
|  | `master` | C++03/Boost 1.57 | **Works.** |  
  
Have you tried running the examples recently? Do you see different behavior?  
If no, then the conclusion is that, currently, Fibers with Asio work only in the `master` branch with C++03.

---

## Comment 10

> Username: olk  
> Created at: 2015-02-18 11:59:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74853183  

With branch master of boost.asio all asio examples work as expected (branch develop C++14)

---

## Comment 11

> Username: erakadjiev  
> Created at: 2015-02-19 02:30:49 UTC  
> Updated at: 2015-02-19 04:23:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-74989708  

Thanks for the reply!  
  
I'm also using Boost Asio master (commit 092171c). I tried the examples built using Boost Build, and they indeed work. They are statically linked, while I was linking Boost Fiber and the other required libraries dynamically. It turned out that this is what leads to the subtle failures.   
  
So the result of `g++ -std=c++14 echo_server.cpp -o echo_server -lboost_fiber -lboost_context -lboost_thread -lboost_system` is that the server compiles, starts, but never gets a successful connection.  
By adding `-static` (and `-pthread`), that is `g++ -std=c++14 -static -pthread echo_server.cpp -o echo_server -lboost_fiber -lboost_context -lboost_thread -lboost_system`, the server works well.  
The echo_client works both with static and dynamic linking.  
  
What is the correct way to compile applications using Boost Fiber? It would be good to add a short note in the readme about this.  
  
PS: if I add `-fsplit-stack -DBOOST_USE_SEGMENTED_STACKS` to the g++ command, the compilation fails. Just `-fsplit-stack` works. By skimming through the errors, it seems that `-DBOOST_USE_SEGMENTED_STACKS` invalidates the `-std=c++14` flag.

---

## Comment 12

> Username: olk  
> Created at: 2015-02-19 07:26:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-75008918  

using shared libraries works for me - changed Jamfile.v2 to <link>shared + put libboost_context.so in search path + tell the linked to use libboost_context.so -> asio examples do work  
  
using segmented stacks requires to apply property segmented-stacks=on

---

## Comment 13

> Username: erakadjiev  
> Created at: 2015-02-19 10:18:59 UTC  
> Updated at: 2015-02-20 01:08:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/29#issuecomment-75028023  

I'm not going to use Boost Build for my application, and I'm not familiar with it either, so I'd like to make this work with g++ directly.  
  
However, I tried what happens if I use Boost Build for the examples. As you wrote, they work.  
I realized that by default the `debug` variant is built with b2, while with g++ I was using `release` libs. After trying both, it seems the `debug` shared library of Boost Fiber works, the `release` doesn't.  
  
To reproduce:  
  
<ol start="0">  
<li>Pre-built Boost library dependencies are in linker search path</li>  
<li><code>cd /modboost-folder/libs/fiber/examples</code></li>  
<li>Change Jamfile.v2:  
<pre>  
-      &lt;link&gt;static  
+      &lt;link&gt;shared  
+      &lt;linkflags&gt;-lboost_context  
+      &lt;linkflags&gt;-lboost_thread  
</pre>  
</li>  
<li><code>debug</code> build:  
<ul>  
<li><pre>b2 toolset=gcc cxxflags="-std=c++14" address-model=64 architecture=x86 <b>debug</b>  
cd /modboost-folder/bin.v2/libs/fiber/examples/gcc-4.9.2/<b>debug</b>/address-model-64/architecture-x86/threading-multi/asio</pre></li>  
<li>In one terminal <code>./echo_server 1234</code>, in another <code>./echo_client 127.0.0.1 1234</code></li>  
<li><b>Works</b></li>  
</ul>  
</li>  
<li><code>release</code> build  
<ul>  
<li><pre>b2 toolset=gcc cxxflags="-std=c++14" address-model=64 architecture=x86 <b>release</b>  
cd /modboost-folder/bin.v2/libs/fiber/examples/gcc-4.9.2/<b>release</b>/address-model-64/architecture-x86/threading-multi/asio</pre></li>  
<li>In one terminal <code>./echo_server 1234</code>, in another <code>./echo_client 127.0.0.1 1234</code></li>  
<li><b>Doesn't work</b></li>  
</ul>  
</li>  
</ol>
