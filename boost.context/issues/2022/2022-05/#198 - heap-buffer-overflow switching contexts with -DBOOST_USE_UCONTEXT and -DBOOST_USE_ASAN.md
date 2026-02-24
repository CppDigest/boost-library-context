# #198 - heap-buffer-overflow switching contexts with -DBOOST_USE_UCONTEXT and -DBOOST_USE_ASAN [Closed]

> Username: elfprince13  
> Created at: 2022-05-05 16:48:26 UTC  
> Updated at: 2022-12-17 10:39:56 UTC  
> Closed at: 2022-12-17 10:39:56 UTC  
> Url: https://github.com/boostorg/context/issues/198  

On boost 1.78, built with context-impl=ucontext.  
  
Reproducer with log:  
  
```console  
ubuntu@host:~$ git clone --recursive --branch crash-reproducer-2022.05.05 https://github.com/Geopipe/Cxx-Bidirectional-Coroutines.git  
Cloning into 'Cxx-Bidirectional-Coroutines'...  
remote: Enumerating objects: 149, done.  
remote: Counting objects: 100% (82/82), done.  
remote: Compressing objects: 100% (62/62), done.  
remote: Total 149 (delta 32), reused 62 (delta 16), pack-reused 67  
Receiving objects: 100% (149/149), 48.66 KiB | 3.74 MiB/s, done.  
Resolving deltas: 100% (55/55), done.  
Submodule 'deps/Functional-Cxx' (ssh://git@github.com/Geopipe/Functional-Cxx.git) registered for path 'deps/Functional-Cxx'  
Cloning into '/home/ubuntu/Cxx-Bidirectional-Coroutines/deps/Functional-Cxx'...  
remote: Enumerating objects: 68, done.          
remote: Counting objects: 100% (68/68), done.          
remote: Compressing objects: 100% (44/44), done.          
remote: Total 68 (delta 24), reused 56 (delta 16), pack-reused 0          
Receiving objects: 100% (68/68), 33.01 KiB | 11.00 MiB/s, done.  
Resolving deltas: 100% (24/24), done.  
Submodule path 'deps/Functional-Cxx': checked out 'c79c1fa55376275349afa618db77db88e4088b53'  
ubuntu@host:~$ cd Cxx-Bidirectional-Coroutines/  
ubuntu@host:~/Cxx-Bidirectional-Coroutines$ mkdir build  
ubuntu@host:~/Cxx-Bidirectional-Coroutines$ cd build/  
ubuntu@host:~/Cxx-Bidirectional-Coroutines/build$ cmake -DCMAKE_BUILD_TYPE=Debug -DCMAKE_CXX_FLAGS="-fsanitize=address -fsanitize=undefined -fsanitize-recover=all  -DBOOST_USE_ASAN -DBOOST_USE_UCONTEXT" ..  
-- The C compiler identification is Clang 12.0.0  
-- The CXX compiler identification is Clang 12.0.0  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: /usr/bin/cc - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found Boost: /usr/local/lib/cmake/Boost-1.78.0/BoostConfig.cmake (found suitable version "1.78.0", minimum required is "1.65") found components: context   
-- base_path = /home/ubuntu/Cxx-Bidirectional-Coroutines/include/cxx-bidirectional-coroutines  
-- Found Boost: /usr/local/lib/cmake/Boost-1.78.0/BoostConfig.cmake (found suitable version "1.78.0", minimum required is "1.65")    
-- base_path = /home/ubuntu/Cxx-Bidirectional-Coroutines/deps/Functional-Cxx/include/functional-cxx  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /home/ubuntu/Cxx-Bidirectional-Coroutines/build  
username@host:~/Cxx-Bidirectional-Coroutines/build$ make testcoro  
[ 50%] Building CXX object example/CMakeFiles/testcoro.dir/main.cpp.o  
[100%] Linking CXX executable testcoro  
[100%] Built target testcoro  
ubuntu@host:~/Cxx-Bidirectional-Coroutines/build$ example/testcoro  
Fibs  
==1832274==WARNING: ASan doesn't fully support makecontext/swapcontext functions and may produce false positives in some cases!  
=================================================================  
==1832274==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x6190000009a0 at pc 0x0000004e2a4c bp 0x7f258dbf5590 sp 0x7f258dbf5588  
READ of size 8 at 0x6190000009a0 thread T0  
    #0 0x4e2a4b in boost::context::detail::activation_record::resume() /usr/local/include/boost/context/continuation_ucontext.hpp:126:65  
    #1 0x4da4ca in boost::context::continuation::resume() && /usr/local/include/boost/context/continuation_ucontext.hpp:410:74  
    #2 0x4ded60 in boost::context::continuation::resume() & /usr/local/include/boost/context/continuation_ucontext.hpp:403:35  
    #3 0x4f191c in com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<void>::Yield::operator()() /home/ubuntu/Cxx-Bidirectional-Coroutines/include/cxx-bidirectional-coroutines/bidirectional-coroutine.hpp:240:18  
    #4 0x4f162d in Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)::operator()(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&) const /home/ubuntu/Cxx-Bidirectional-Coroutines/example/main.cpp:17:7  
    #5 0x4f0490 in void com::geopipe::functional::detail::FinishCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int> >::apply<Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&) const, 0>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&) const&) /home/ubuntu/Cxx-Bidirectional-Coroutines/include/cxx-bidirectional-coroutines/bidirectional-coroutine.hpp:308:7  
    #6 0x4eff17 in boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long)::'lambda'(boost::context::continuation&&)::operator()(boost::context::continuation&&) const /home/ubuntu/Cxx-Bidirectional-Coroutines/include/cxx-bidirectional-coroutines/bidirectional-coroutine.hpp:53:8  
    #7 0x4efcd3 in com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int> std::__invoke_impl<boost::context::continuation, boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long)::'lambda'(boost::context::continuation&&)&, boost::context::continuation>(std::__invoke_other, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&&, boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long)::'lambda'(boost::context::continuation&&)&...) /usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/bits/invoke.h:60:14  
    #8 0x4efb43 in std::__invoke_result<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)...>::type std::__invoke<boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long)::'lambda'(boost::context::continuation&&)&, boost::context::continuation>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&&...) /usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/bits/invoke.h:95:14  
    #9 0x4efa13 in std::invoke_result<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)...>::type std::invoke<boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long)::'lambda'(boost::context::continuation&&)&, boost::context::continuation>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&&...) /usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/functional:81:14  
    #10 0x4ef255 in boost::context::detail::capture_record<boost::context::continuation, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long)::'lambda'(boost::context::continuation&&)>::run() /usr/local/include/boost/context/continuation_ucontext.hpp:258:17  
    #11 0x4d3a12 in void boost::context::detail::entry_func<boost::context::detail::capture_record<boost::context::continuation, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long)::'lambda'(boost::context::continuation&&)> >(void*) /usr/local/include/boost/context/continuation_ucontext.hpp:68:13  
    #12 0x7f2590a1350f  (/usr/lib/x86_64-linux-gnu/libc.so.6+0x5b50f)  
  
0x6190000009a0 is located 8 bytes to the right of 1048-byte region [0x619000000580,0x619000000998)  
allocated by thread T0 here:  
    #0 0x4ca96d in operator new(unsigned long) (/home/ubuntu/Cxx-Bidirectional-Coroutines/build/example/testcoro+0x4ca96d)  
    #1 0x7f2590f2d34e in boost::context::detail::activation_record::current() (/usr/local/lib/libboost_context.so.1.78.0+0x234e)  
    #2 0x4da4ca in boost::context::continuation::resume() && /usr/local/include/boost/context/continuation_ucontext.hpp:410:74  
    #3 0x4ed9f5 in boost::context::continuation boost::context::callcc<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long)::'lambda'(boost::context::continuation&&)>(std::allocator_arg_t, com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&&) /usr/local/include/boost/context/continuation_ucontext.hpp:516:70  
    #4 0x4ebd53 in boost::context::continuation com::geopipe::functional::detail::_CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::startCoroutine<com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>&, Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)&, unsigned long) /home/ubuntu/Cxx-Bidirectional-Coroutines/include/cxx-bidirectional-coroutines/bidirectional-coroutine.hpp:51:14  
    #5 0x4eb8eb in com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::BidirectionalCoroutine<Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&)>(Fibonacci::Fibonacci()::'lambda'(com::geopipe::functional::CoroutineContext<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >::BidirectionalCoroutine<int>::Yield&), unsigned long) /home/ubuntu/Cxx-Bidirectional-Coroutines/include/cxx-bidirectional-coroutines/bidirectional-coroutine.hpp:168:15  
    #6 0x4eb6a3 in Fibonacci::Fibonacci() /home/ubuntu/Cxx-Bidirectional-Coroutines/example/main.cpp:14:16  
    #7 0x4d3b1b in std::_MakeUniq<Fibonacci>::__single_object std::make_unique<Fibonacci>() /usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/bits/unique_ptr.h:857:34  
    #8 0x4cd55b in main /home/ubuntu/Cxx-Bidirectional-Coroutines/example/main.cpp:47:49  
    #9 0x7f25909dc0b2 in __libc_start_main (/usr/lib/x86_64-linux-gnu/libc.so.6+0x240b2)  
  
SUMMARY: AddressSanitizer: heap-buffer-overflow /usr/local/include/boost/context/continuation_ucontext.hpp:126:65 in boost::context::detail::activation_record::resume()  
Shadow bytes around the buggy address:  
  0x0c327fff80e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c327fff80f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c327fff8100: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c327fff8110: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c327fff8120: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x0c327fff8130: 00 00 00 fa[fa]fa fa fa fa fa fa fa fa fa fa fa  
  0x0c327fff8140: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c327fff8150: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c327fff8160: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c327fff8170: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c327fff8180: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
  Shadow gap:              cc  
==1832274==ABORTING  
ubuntu@host:~/Cxx-Bidirectional-Coroutines/build$   
```  
  
Just the commands needed to reproduce:  
```bash  
git clone --recursive --branch crash-reproducer-2022.05.05 https://github.com/Geopipe/Cxx-Bidirectional-Coroutines.git  
cd Cxx-Bidirectional-Coroutines/  
mkdir build  
cd build/  
cmake -DCMAKE_BUILD_TYPE=Debug -DCMAKE_CXX_FLAGS="-fsanitize=address -fsanitize=undefined -fsanitize-recover=all  -DBOOST_USE_ASAN -DBOOST_USE_UCONTEXT" ..  
make testcoro  
./example/testcoro  
```

---

## Comment 1

> Username: olk  
> Created at: 2022-12-17 10:39:56 UTC  
> Url: https://github.com/boostorg/context/issues/198#issuecomment-1356167389  

should be fixed by " fix ucontext for MacOS #213 "
