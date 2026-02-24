# #674 - Failure to compile on MacOS X [Closed]

> Username: rberlich  
> Created at: 2017-07-24 16:28:45 UTC  
> Updated at: 2017-08-16 00:05:15 UTC  
> Closed at: 2017-08-16 00:05:15 UTC  
> Url: https://github.com/boostorg/beast/issues/674  

### Version of Beast  
  
Release 85, downloaded 5 Minutes ago  
  
### Steps necessary to reproduce the problem  
  
On MacOS X (10.12.6, latest release, all updates) compile Boost 1.64 in C++14 mode:  
  
- `./bootstrap.sh  --prefix=/opt/boost`  
- `./b2 cxxflags="-std=c++14"`  
- `./b2 install`  
  
Compile beast with the following commands (from zip archive as downloaded from github):  
  
- `unzip Beast-develop.zip`  
- In the Beast-directory create a build directory  
- Inside the build-directory: `cmake -DBOOST_ROOT=/opt/boost ..`  
- make  
  
### Error message  
  
The above command yields the error message  
  
```  
[ 27%] Linking CXX executable core-tests  
Undefined symbols for architecture x86_64:  
  "boost::coroutines::detail::coroutine_context::jump(boost::coroutines::detail::coroutine_context&, long, bool)", referenced from:  
      boost::coroutines::detail::push_coroutine_object<boost::coroutines::pull_coroutine<void>, void, boost::asio::detail::coro_entry_point<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running>, void beast::test::enable_yield_to::spawn<beast::buffered_read_stream_test::run()::'lambda'(boost::asio::basic_yield_context<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running> >)&>(beast::buffered_read_stream_test::run()::'lambda'(boost::asio::basic_yield_context<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running> >)&&&)::'lambda'(boost::asio::basic_yield_context<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running> >)>&, boost::coroutines::basic_standard_stack_allocator<boost::coroutines::stack_traits> >::run() in buffered_read_stream.cpp.o  
      boost::coroutines::detail::pull_coroutine_impl<void>::pull() in buffered_read_stream.cpp.o  
      boost::coroutines::detail::push_coroutine_impl<void>::unwind_stack() in buffered_read_stream.cpp.o  
      boost::coroutines::detail::push_coroutine_impl<void>::push() in buffered_read_stream.cpp.o  
      boost::coroutines::detail::push_coroutine_object<boost::coroutines::pull_coroutine<void>, void, boost::asio::detail::coro_entry_point<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running>, void beast::test::enable_yield_to::spawn<beast::http::doc_core_samples_test::testRead()::'lambda'(boost::asio::basic_yield_context<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running> >)&>(beast::http::doc_core_samples_test::testRead()::'lambda'(boost::asio::basic_yield_context<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running> >)&&&)::'lambda'(boost::asio::basic_yield_context<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running> >)>&, boost::coroutines::basic_standard_stack_allocator<boost::coroutines::stack_traits> >::run() in doc_examples.cpp.o  
      boost::coroutines::detail::pull_coroutine_impl<void>::pull() in doc_examples.cpp.o  
      boost::coroutines::detail::push_coroutine_impl<void>::unwind_stack() in doc_examples.cpp.o  
      ...  
  "boost::coroutines::detail::coroutine_context::coroutine_context(void (*)(long), boost::coroutines::detail::preallocated const&)", referenced from:  
      __ZN5boost10coroutines6detail27push_coroutine_context_voidC2INS1_21push_coroutine_objectINS0_14pull_coroutineIvEEvRNS_4asio6detail16coro_entry_pointINS8_15wrapped_handlerINS7_10io_service6strandEPFvvENS8_26is_continuation_if_runningEEEZN5beast4test15enable_yield_to5spawnIRZNSH_25buffered_read_stream_test3runEvEUlNS7_19basic_yield_contextISG_EEE_JEEEvOT_DpOT0_EUlSN_E_EENS0_30basic_standard_stack_allocatorINS0_12stack_traitsEEEEEEERKNS1_12preallocatedEPSQ_ in buffered_read_stream.cpp.o  
      __ZN5boost10coroutines6detail27push_coroutine_context_voidC2INS1_21push_coroutine_objectINS0_14pull_coroutineIvEEvRNS_4asio6detail16coro_entry_pointINS8_15wrapped_handlerINS7_10io_service6strandEPFvvENS8_26is_continuation_if_runningEEEZN5beast4test15enable_yield_to5spawnIRZNSH_4http21doc_core_samples_test8testReadEvEUlNS7_19basic_yield_contextISG_EEE_JEEEvOT_DpOT0_EUlSO_E_EENS0_30basic_standard_stack_allocatorINS0_12stack_traitsEEEEEEERKNS1_12preallocatedEPSR_ in doc_examples.cpp.o  
ld: symbol(s) not found for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
make[2]: *** [test/core/core-tests] Error 1  
make[1]: *** [test/core/CMakeFiles/core-tests.dir/all] Error 2  
make: *** [all] Error 2  
```  
  
So obviously there is some problem with boost coroutine on MacOS. In comparison, building beast on Ubuntu 17.04 works flawlessly.  
  
### All relevant compiler information  
  
```  
$ clang -v  
Apple LLVM version 8.1.0 (clang-802.0.42)  
Target: x86_64-apple-darwin16.7.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
Note: this is the latest release for MacOS.  
  
### Edit 1:  
  
Sometimes with custom Boost installations, there can be a mix-up with "System-installations" of Boost. However, adding `-DBoost_NO_SYSTEM_PATHS=TRUE` to the cmake command does not affect the problem.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-24 16:48:38 UTC  
> Updated at: 2017-07-24 16:49:34 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-317483313  

My apologies but I have absolutely no idea where to even begin to figure this out! I have not tested the CMake on Mac at all. And this might come as bad news but when Beast is merged to Boost in 1.66.0, the only supported CMake configuration for building the tests and examples will be for Visual Studio on Windows.  
  
Could you please try building the Beast tests and examples using b2? Just cd to the root of the beast directory and run "b2" by itself. You might need to set BOOST_ROOT in your environment.  
  
To be clear, Beast should work for you in your own projects, we're only talking about building the tests and examples using the provided CMakeLists.txt.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-07-24 17:09:02 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-317489530  

Looks like Boost is built 32 bit and Beast is 64 bit?

---

## Comment 3

> Username: rberlich  
> Created at: 2017-07-24 17:56:27 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-317503669  

Peter, thanks for the tip. I have now specifically chosen the 64 bit address model (flag `address-model=64` for b2 during Boost compilation and am quite sure that Boost indeed is 64 bit on my system:  
  
```  
$ ./b2 cxxflags="-std=c++14" address-model=64 -j 4  
Performing configuration checks  
  
    - 32-bit                   : no  
    - 64-bit                   : yes  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - x86                      : yes  
```  
  
I have also made sure to choose 64 bit explicitly during Beast compilation via the cmake flag `-DCMAKE_OSX_ARCHITECTURES=x86_64` (btw, one of the things I really hate about cmake is the huge amount of partially overlapping options -- note: off-topic).  
  
The error I'm getting during Beast-compilation, however, remains the same.   
  
I will check whether I'm getting any further by using b2 for Beast as well and will report here on the results.

---

## Comment 4

> Username: HowardHinnant  
> Created at: 2017-07-25 00:27:10 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-317592792  

You might have to fiddle with `inline` and/or optimization.  If when compiling boost, the compiler decided to inline something, and thus not put it in the dylib, and then when compiling the client, decided _not_ to inline the same thing, it would not find it in the dylib.  
  
This is just a theory.  Not sure if it is the cause of this issue.

---

## Comment 5

> Username: rberlich  
> Created at: 2017-07-25 10:32:17 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-317697684  

@Howard: I am actually using about a dozen Boost libraries on the same machine for my own code without problems, albeit not Boost.Coroutine. It might well be that there is something special about Coroutine on the Mac. I do find this difficult to find out, though, as I am only using it indirectly via Beast.   
  
I have meanwhile also tried to compile Boost specifically for C++11 (instead of C++14), as I have found that Beast's Jamroot defaults to C++11. This does not affect the problem, though, and I would be surprised if Beast would be incompatible with C++14 (Vinnie ?).   
  
I am struggling with b2 on the Mac as per Vinnies suggestion, as  
- the meaning of BOOST_ROOT is unclear to me -- it seems to refer to the location of b2 (i.e. the Boost-source directory after bootstrap.sh has run rather than the install dir, but b2 will need the information about installed Boost-libraries. b2 doesn't get installed along with the libraries to the custom install target, but needs access to information in the source dir. So there seems to be a contradiction here.  
- it somehow does not recognize C++11/14-constructs (i.e. seems to compile in C++98 mode, passing cxxflags="-std=c++11" does not help).   
  
In summary, I need to investigate some more here. In any case thanks for the suggestions!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-25 11:47:10 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-317712976  

Beast should work with C++14. I set `-std=c++11` to make sure the compiler gives me an error if anything that requires C++14 or higher slips in, as Beast should only require C++11.

---

## Comment 7

> Username: HowardHinnant  
> Created at: 2017-07-25 14:37:39 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-317758318  

If it helps, here is how I build boost on macOS:  
```  
./bootstrap.sh  
 ./b2 toolset=clang threading=multi runtime-link=static  link=static cxxflags="-stdlib=libc++ -std=c++11" linkflags="-stdlib=libc++" address-model=64  
```

---

## Comment 8

> Username: xsacha  
> Created at: 2017-07-26 11:53:41 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-318031219  

>  have not tested the CMake on Mac at all. And this might come as bad news but when Beast is merged to Boost in 1.66.0, the only supported CMake configuration for building the tests and examples will be for Visual Studio on Windows.  
  
Well it works wonderfully on Mac with CMake here. Tested with pre-built and from scratch (building Boost with CMake too.  
I think this issue is just for b2/bjam.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-07-26 13:54:14 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-318059816  

Whoops.. no idea why I thought CMake was implicated!

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-08-04 23:04:07 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-320371375  

I have added and debugged an OS X target on Travis, so problems with MacOS builds should be detected before getting merged to public branches.  
  
If this is still an issue please let me know what I can do to help. Thanks!

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-08-16 00:05:15 UTC  
> Url: https://github.com/boostorg/beast/issues/674#issuecomment-322622033  

It looks like this is no longer a problem. If it comes up again feel free to re-open this issue or even better create a new one - thanks!
