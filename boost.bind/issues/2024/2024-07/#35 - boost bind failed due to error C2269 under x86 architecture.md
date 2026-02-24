# #35 - [msvc] boost bind failed due to error C2269 under x86 architecture [Closed]

> Username: Zhaojun-Liu  
> Created at: 2024-07-09 11:27:17 UTC  
> Updated at: 2024-07-10 00:28:22 UTC  
> Closed at: 2024-07-09 18:06:12 UTC  
> Url: https://github.com/boostorg/bind/issues/35  

Hi,  
We use msvc to build boost and boost libs, and lib bind failed due to error C2269 under x86 architecture. Can you take a look? Thanks.  
  
**Repro steps:**  
1. Open VS2022 x86 Native Tools command.  
2. Run command: `cl bind_stdcall_mf_test.txt /TP /c /EHsc`  
3. Run command: `cl bind_fastcall_mf_test.txt /TP /c /EHsc`  
  
**Expected:** step 2 and 3 build ok.  
  
**Actual:**  
for step 2:  
```  
bind_stdcall_mf_test.i  
.\boost/bind/bind.hpp(753): error C2269: cannot create a pointer or reference to a qualified function type (requires pointer-to-member)  
.\boost/bind/bind.hpp(753): note: the template instantiation context (the oldest one first) is  
libs\bind\test\bind_stdcall_mf_test.cpp(80): note: see reference to function template instantiation 'boost::_bi::bind_t<_bi::dm_result<MT::* ,A1>::type,boost::_mfi::dm<M,T>,_bi::list_av<A1>::type> boost::bind(M T::* ,A1)' being compiled  
libs\bind\test\bind_stdcall_mf_test.cpp(80): note: see reference to class template instantiation 'boost::_bi::dm_result<int (__stdcall X::* )(void) const,X *>' being compiled  
.\boost/bind/bind.hpp(795): note: see reference to class template instantiation 'boost::_bi::add_cref<Pm,1>' being compiled  
        with  
        [  
            Pm=int (__stdcall X::* )(void) const  
        ]  
```  
  
for step 3:  
```  
bind_fastcall_mf_test.i  
.\boost/bind/bind.hpp(753): error C2269: cannot create a pointer or reference to a qualified function type (requires pointer-to-member)  
.\boost/bind/bind.hpp(753): note: the template instantiation context (the oldest one first) is  
libs\bind\test\bind_fastcall_mf_test.cpp(80): note: see reference to function template instantiation 'boost::_bi::bind_t<_bi::dm_result<MT::* ,A1>::type,boost::_mfi::dm<M,T>,_bi::list_av<A1>::type> boost::bind(M T::* ,A1)' being compiled  
libs\bind\test\bind_fastcall_mf_test.cpp(80): note: see reference to class template instantiation 'boost::_bi::dm_result<void (__fastcall X::* )(void) const,X *>' being compiled  
.\boost/bind/bind.hpp(803): note: see reference to class template instantiation 'boost::_bi::add_cref<Pm,1>' being compiled  
        with  
        [  
            Pm=void (__fastcall X::* )(void) const  
        ]  
```  
**Note:** This issue may be reproduced in the next released version of VS.  
  
[bind_fastcall_mf_test.txt](https://github.com/user-attachments/files/16142558/bind_fastcall_mf_test.txt)  
[bind_stdcall_mf_test.txt](https://github.com/user-attachments/files/16142559/bind_stdcall_mf_test.txt)

---

## Comment 1

> Username: pdimov  
> Created at: 2024-07-10 00:28:21 UTC  
> Url: https://github.com/boostorg/bind/issues/35#issuecomment-2219157300  

Should be fixed by https://github.com/boostorg/bind/commit/769479a9640c5f8e42e417c1192cdb0f324202b1.
