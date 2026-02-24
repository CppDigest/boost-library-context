# #152 - Linker errors are produced when compiling a minimal example with GNU ld or GNU gold linkers [Closed]

> Username: arun5309  
> Created at: 2024-01-11 03:52:26 UTC  
> Updated at: 2024-12-13 03:14:45 UTC  
> Closed at: 2024-12-13 03:14:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152  

Linker errors are produced when compiling a minimal example with GNU ld or GNU gold linkers both on my local machine and on godbolt.org (https://godbolt.org/z/fhGME8f6Y). It builds without any issues and runs as expected when using lld or mold linker. I have tested with gcc 12.2 and clang 15 (using libstdc++) on Void Linux (kernel version 6.6.10) on my local machine.  
  
Also, refer to the issue opened by me here https://github.com/compiler-explorer/compiler-explorer/issues/5960.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-01-11 03:55:27 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-1886185186  

I wonder why it works on `ld` and `gold`, when you don't actually link against the cobalt lib.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-01-11 04:33:57 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-1886227293  

Can you please provide the linking command on your local machine? Because you're not linking against `boost_cobalt` on the compiler explorer.

---

## Comment 3

> Username: arun5309  
> Created at: 2024-01-11 04:34:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-1886227795  

> I wonder why it works on `ld` and `gold`, when you don't actually link against the cobalt lib.  
  
It actually doesn't work with `ld` or `gold` but works with `mold` or `lld`.   
I should have also clearly stated that the **examples provided build fine when using cmake with the provided cmake files (even with `ld`)** but results in linker errors when I manually invoke the compiler with a command such as:-  
  
```  
g++ ../cobalt/build/libboost_cobalt.a basic.cpp -o basic -I../cobalt/include -std=c++20 -lboost_system -fuse-ld=gold  
```  
  
**'libboost_cobalt.a' is getting linked here.** I checked out into `tags/boost-1.84.0` before building cobalt in `Release` mode. I also have `boost 1.83` on my system from the void repository. Also, I don't need the `-lboost_system` flag to build the minimal example when using `mold` or `lld`.  
  
Also, `ld` and `gold` are producing similar errors locally as the godbolt.org link above. I am copy pasting the errors I got locally here as well.  
  
```  
/tmp/ccOHnbJV.o:basic.cpp:function co_main(co_main(int, char**)::_Z7co_mainiPPc.Frame*) [clone .actor]: error: undefined reference to 'boost::cobalt::detail::main_promise::final_suspend()'  
/tmp/ccOHnbJV.o:basic.cpp:function main::reset_res::operator()(std::pmr::memory_resource*): error: undefined reference to 'boost::cobalt::this_thread::set_default_resource(std::pmr::memory_resource*)'  
/tmp/ccOHnbJV.o:basic.cpp:function main: error: undefined reference to 'boost::cobalt::this_thread::set_default_resource(std::pmr::memory_resource*)'  
/tmp/ccOHnbJV.o:basic.cpp:function main: error: undefined reference to 'boost::cobalt::detail::main_promise::run_main(boost::cobalt::main)'  
/tmp/ccOHnbJV.o:basic.cpp:function bool boost::cobalt::detail::promise_receiver<void>::awaitable::await_suspend<boost::cobalt::detail::fork::promise_type>(std::__n4861::coroutine_handle<boost::cobalt::detail::fork::promise_type>): error: undefined reference to 'boost::cobalt::detail::already_awaited()'  
/tmp/ccOHnbJV.o:basic.cpp:function boost::cobalt::detail::promise_receiver<void>::awaitable::interrupt_await() &: error: undefined reference to 'boost::cobalt::detail::detached_exception()'  
collect2: error: ld returned 1 exit status  
```  
The linker versions used locally are `ld 2.39` (binutils 2.39), `gold 1.16` (binutils 2.39), `lld 15.0.7` and `mold 2.4.0`.

---

## Comment 4

> Username: arun5309  
> Created at: 2024-01-11 04:45:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-1886235998  

> Can you please provide the linking command on your local machine? Because you're not linking against `boost_cobalt` on the compiler explorer.  
  
I have provided the compile command on the above comment. I haven't linked against `boost_cobalt` explicitly on the compiler explorer code as usually selecting the library links it automatically (so no need for option like `-lboost_cobalt`). Also, I am still getting errors on compiler explorer passing the `-lboost_cobalt` option explicitly.

---

## Comment 5

> Username: arun5309  
> Created at: 2024-01-11 08:55:08 UTC  
> Updated at: 2024-01-11 08:55:38 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-1886653817  

It works with `gold` and `ld` if the order of `libboost_cobalt.a` and `basic.cpp` is swapped. The following command works:-  
  
`g++ basic.cpp ../cobalt/build/libboost_cobalt.a -o basic -I../cobalt/include -std=c++20 -lboost_system -fuse-ld=gold`  
  
Not sure if this is a bug but it causes issues like when trying to use it in tools like compiler explorer.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2024-01-16 01:40:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-1892948355  

It might be because the boost_cobalt target is conditional. I.e. it's not available if you're building with a standard below C++20.

---

## Comment 7

> Username: mitsubishirgb  
> Created at: 2024-09-06 14:03:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-2334131507  

@klemens-morgenstern   
how do i obtain the libboost_cobalt.lib i am kinda new to cpp so i don't know how to get it, i tried to run cmake i got openssl unrecognized errors with correct paths but idk ????

---

## Comment 8

> Username: mitsubishirgb  
> Created at: 2024-09-06 15:04:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-2334258971  

cmake -G "Visual Studio 17 2022" .. -DBOOST_ROOT="C:/libs/boost_1_86_0" -DBoost_NO_SYSTEM_PATHS=ON -DBoost_INCLUDE_DIR="C:/libs/boost_1_86_0" -DBoost_LIBRARY_DIR="C:/libs/boost_1_86_0/stage/lib" -DBoost_SYSTEM_LIBRARY="/libs/boost_1_86_0/stage/lib/libboost_system-vc143-mt-x64-1_86.lib" -DOPENSSL_ROOT_DIR="C:/libs/OpenSSL-Win64" -DOPENSSL_INCLUDE_DIR="C:/libs/OpenSSL-Win64/include" -DOPENSSL_SSL_LIBRARY="C:/libs/OpenSSL-Win64/lib/VC/x64/MT/libssl.lib" -DOPENSSL_CRYPTO_LIBRARY="C:/libs/OpenSSL-Win64/lib/VC/x64/MT/libcrypto.lib"  
  
CMake Error at example/CMakeLists.txt:44 (target_link_libraries):  
  Target "boost_cobalt_example_channel" links to:  
  
    OpenSSL::SSL  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2024-09-06 15:51:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-2334355234  

You should build cobalt as part of boost. The above looks like you're doing something different.   
  
Here are more details on how to build boost. https://www.boost.io/doc/user-guide/getting-started.html

---

## Comment 10

> Username: mitsubishirgb  
> Created at: 2024-09-06 15:57:07 UTC  
> Url: https://github.com/boostorg/cobalt/issues/152#issuecomment-2334364167  

> You should build cobalt as part of boost. The above looks like you're doing something different.  
>   
> Here are more details on how to build boost. https://www.boost.io/doc/user-guide/getting-started.html  
  
i replaced the libssl and crypto on the lib directoy of openssl and the code above worked successfully appreantly i attained the libboost_cobalt.lib and it compiles with no errors
