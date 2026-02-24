# #20 - Boost failed to compile test in coroutine2 lib due to the error C2039 [Closed]

> Username: shanshan0309  
> Created at: 2018-04-09 06:50:08 UTC  
> Updated at: 2018-04-12 01:21:17 UTC  
> Closed at: 2018-04-12 01:21:17 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/20  

We tried to build and run coroutine2 test for Boost with VS2017 Update 5 on Windows. It failed to build due to the error C2039: 'forced_unwind': is not a member of 'boost::context::detail'. Could you please help take a look at this? Thank you!  
  
**Reproduce steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. Open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\coroutine2\test  
  
**Expected result:**  
All tests passed  
  
**Actual result:**  
Whole log file please see attachment.  
[log_x86_test_coroutine2.log](https://github.com/boostorg/coroutine2/files/1888684/log_x86_test_coroutine2.log)  
.\boost/coroutine2/detail/pull_control_block_cc.ipp(85): **error C2039: 'forced_unwind': is not a member of 'boost::context::detail'**  
.\boost/context/detail/invoke.hpp(24): note: see declaration of 'boost::context::detail'  
.\boost/coroutine2/detail/create_control_block.ipp(51): note: see reference to function template instantiation 'boost::coroutines2::detail::pull_coroutine<T>::control_block::control_block<_Ty,void(__cdecl &)(boost::coroutines2::detail::push_coroutine<T> &)>(boost::context::preallocated,StackAllocator &&,Fn)' being compiled  
        with  
        [  
            T=int,  
            _Ty=boost::coroutines2::default_stack,  
            StackAllocator=boost::coroutines2::default_stack,  
            Fn=void (__cdecl &)(boost::coroutines2::detail::push_coroutine<int> &)  
        ]  
.\boost/coroutine2/detail/create_control_block.ipp(50): note: see reference to function template instantiation 'boost::coroutines2::detail::pull_coroutine<T>::control_block::control_block<_Ty,void(__cdecl &)(boost::coroutines2::detail::push_coroutine<T> &)>(boost::context::preallocated,StackAllocator &&,Fn)' being compiled  
        with  
        [  
            T=int,  
            _Ty=boost::coroutines2::default_stack,  
            StackAllocator=boost::coroutines2::default_stack,  
            Fn=void (__cdecl &)(boost::coroutines2::detail::push_coroutine<int> &)  
        ]  
.\boost/coroutine2/detail/pull_coroutine.ipp(54): note: see reference to function template instantiation 'ControlBlock *boost::coroutines2::detail::create_control_block<boost::coroutines2::detail::pull_coroutine<T>::control_block,_Ty,void(__cdecl &)(boost::coroutines2::detail::push_coroutine<T> &)>(StackAllocator &&,Fn)' being compiled  
        with  
        [  
            ControlBlock=boost::coroutines2::detail::pull_coroutine<int>::control_block,  
            T=int,  
            _Ty=boost::coroutines2::default_stack,  
            StackAllocator=boost::coroutines2::default_stack,  
            Fn=void (__cdecl &)(boost::coroutines2::detail::push_coroutine<int> &)  
        ]  
.\boost/coroutine2/detail/pull_coroutine.ipp(48): note: see reference to function template instantiation 'boost::coroutines2::detail::pull_coroutine<T>::pull_coroutine<boost::coroutines2::default_stack,void(__cdecl &)(boost::coroutines2::detail::push_coroutine<T> &)>(StackAllocator &&,Fn)' being compiled  
        with  
        [  
            T=int,  
            StackAllocator=boost::coroutines2::default_stack,  
            Fn=void (__cdecl &)(boost::coroutines2::detail::push_coroutine<int> &)  
        ]  
.\boost/coroutine2/detail/pull_coroutine.ipp(48): note: see reference to function template instantiation 'boost::coroutines2::detail::pull_coroutine<T>::pull_coroutine<boost::coroutines2::default_stack,void(__cdecl &)(boost::coroutines2::detail::push_coroutine<T> &)>(StackAllocator &&,Fn)' being compiled  
        with  
        [  
            T=int,  
            StackAllocator=boost::coroutines2::default_stack,  
            Fn=void (__cdecl &)(boost::coroutines2::detail::push_coroutine<int> &)  
        ]  
libs\coroutine2\test\test_coroutine.cpp(237): note: see reference to function template instantiation 'boost::coroutines2::detail::pull_coroutine<T>::pull_coroutine<void(__cdecl &)(boost::coroutines2::detail::push_coroutine<T> &),void>(Fn)' being compiled  
        with  
        [  
            T=int,  
            Fn=void (__cdecl &)(boost::coroutines2::detail::push_coroutine<int> &)  
        ]  
libs\coroutine2\test\test_coroutine.cpp(237): note: see reference to function template instantiation 'boost::coroutines2::detail::pull_coroutine<T>::pull_coroutine<void(__cdecl &)(boost::coroutines2::detail::push_coroutine<T> &),void>(Fn)' being compiled  
        with  
        [  
            T=int,  
            Fn=void (__cdecl &)(boost::coroutines2::detail::push_coroutine<int> &)  
        ]

---

## Comment 1

> Username: shanshan0309  
> Created at: 2018-04-12 01:21:16 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/20#issuecomment-380644544  

This issue has been fixed on the latest revision.
