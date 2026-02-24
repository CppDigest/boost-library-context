# #16 Fix Windows call conventions tests [Closed]

> Username: Kojoley  
> Created at: 2019-04-01 15:53:05 UTC  
> Updated at: 2019-04-23 17:19:52 UTC  
> Closed at: 2019-04-23 17:19:52 UTC  
> Url: https://github.com/boostorg/bind/pull/16  

Currently the tests incorrectly limited to MSVC and also run on 64bit build.  
After the change 32bit Clang and MinGW will be tested, and 64bit MSVC will be skipped.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-04-01 15:56:05 UTC  
> Url: https://github.com/boostorg/bind/pull/16#issuecomment-478638159  

Probably it is worth adding MinGW and Clang Windows builds. There are two fails on 32bit Clang:  
  
```  
compile-c-c++ bin.v2\libs\bind\test\bind_stdcall_mf_test.test\clang-win-7.0.1\debug\threading-multi\bind_stdcall_mf_test.obj  
In file included from libs\bind\test\bind_stdcall_mf_test.cpp:30:  
In file included from .\boost/bind.hpp:22:  
.\boost/bind/bind.hpp(2277,21):  error: reference to function type 'int () __attribute__((stdcall)) const' cannot have 'const' qualifier  
    typedef M const & type;  
                    ^  
.\boost/bind/bind.hpp(2323,22):  note: in instantiation of template class 'boost::_bi::add_cref<int (X::*)() __attribute__((stdcall)) const, 1>' requested here  
    typedef typename add_cref< Pm, 1 >::type type;  
                     ^  
.\boost/bind/bind.hpp(2337,19):  note: in instantiation of template class 'boost::_bi::dm_result<int (X::*)() __attribute__((stdcall)) const, X *>' requested here  
    typename _bi::dm_result< M T::*, A1 >::type,  
                  ^  
libs\bind\test\bind_stdcall_mf_test.cpp(89,5):  note: while substituting deduced template arguments into function template 'bind' [with A1 = X *, M = int () __attribute__((stdcall)) const, T = X]  
    bind(&X::g0, &x)();  
    ^  
1 error generated.  
  
     "clang-cl.exe" -m32 @"bin.v2\libs\bind\test\bind_stdcall_mf_test.test\clang-win-7.0.1\debug\threading-multi\bind_stdcall_mf_test.obj.rsp"  
  
...failed compile-c-c++ bin.v2\libs\bind\test\bind_stdcall_mf_test.test\clang-win-7.0.1\debug\threading-multi\bind_stdcall_mf_test.obj...  
...skipped <pbin.v2\libs\bind\test\bind_stdcall_mf_test.test\clang-win-7.0.1\debug\threading-multi>bind_stdcall_mf_test.exe for lack of <pbin.v2\libs\bind\test\bind_stdcall_mf_test.test\clang-win-7.0.1\debug\threading-multi>bind_stdcall_mf_test.obj...  
...skipped <pbin.v2\libs\bind\test\bind_stdcall_mf_test.test\clang-win-7.0.1\debug\threading-multi>bind_stdcall_mf_test.run for lack of <pbin.v2\libs\bind\test\bind_stdcall_mf_test.test\clang-win-7.0.1\debug\threading-multi>bind_stdcall_mf_test.exe...  
compile-c-c++ bin.v2\libs\bind\test\bind_fastcall_mf_test.test\clang-win-7.0.1\debug\threading-multi\bind_fastcall_mf_test.obj  
In file included from libs\bind\test\bind_fastcall_mf_test.cpp:30:  
In file included from .\boost/bind.hpp:22:  
.\boost/bind/bind.hpp(2277,21):  error: reference to function type 'void () __attribute__((fastcall)) const' cannot have 'const' qualifier  
    typedef M const & type;  
                    ^  
.\boost/bind/bind.hpp(2323,22):  note: in instantiation of template class 'boost::_bi::add_cref<void (X::*)() __attribute__((fastcall)) const, 1>' requested here  
    typedef typename add_cref< Pm, 1 >::type type;  
                     ^  
.\boost/bind/bind.hpp(2337,19):  note: in instantiation of template class 'boost::_bi::dm_result<void (X::*)() __attribute__((fastcall)) const, X *>' requested here  
    typename _bi::dm_result< M T::*, A1 >::type,  
                  ^  
libs\bind\test\bind_fastcall_mf_test.cpp(89,5):  note: while substituting deduced template arguments into function template 'bind' [with A1 = X *, M = void () __attribute__((fastcall)) const, T = X]  
    bind(&X::g0, &x)();  
    ^  
1 error generated.  
  
     "clang-cl.exe" -m32 @"bin.v2\libs\bind\test\bind_fastcall_mf_test.test\clang-win-7.0.1\debug\threading-multi\bind_fastcall_mf_test.obj.rsp"  
```

---
