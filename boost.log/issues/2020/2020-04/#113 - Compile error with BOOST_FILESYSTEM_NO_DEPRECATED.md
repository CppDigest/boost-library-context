# #113 - Compile error with BOOST_FILESYSTEM_NO_DEPRECATED [Closed]

> Username: Nekto89  
> Created at: 2020-04-03 16:40:46 UTC  
> Updated at: 2020-04-03 18:54:58 UTC  
> Closed at: 2020-04-03 18:54:58 UTC  
> Url: https://github.com/boostorg/log/issues/113  

Happens during compilation using Conan with boost::filesystem_no_deprecated=True which adds -DBOOST_FILESYSTEM_NO_DEPRECATED=1  
  
https://www.boost.org/doc/libs/1_72_0/libs/filesystem/doc/deprecated.html  
  
```  
libs\log\src\text_file_backend.cpp(77): error C2039: 'filesystem_error': is not a member of 'boost::filesystem'  
  
.\boost/filesystem/convenience.hpp(29): note: see declaration of 'boost::filesystem'  
  
libs\log\src\text_file_backend.cpp(77): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
  
libs\log\src\text_file_backend.cpp(77): error C2146: syntax error: missing ';' before identifier 'filesystem_error'  
  
libs\log\src\text_file_backend.cpp(77): warning C4091: '': ignored on left of 'int' when no variable is declared  
  
libs\log\src\text_file_backend.cpp(800): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::log::v2s_mt_nt6::sinks::`anonymous-namespace'::filesystem_error'  
  
libs\log\src\text_file_backend.cpp(803): note: The initializer contains too many elements  
  
libs\log\src\text_file_backend.cpp(800): error C2672: 'boost::exception_detail::throw_exception_': no matching overloaded function found  
  
libs\log\src\text_file_backend.cpp(919): error C2039: 'directory_iterator': is not a member of 'boost::filesystem'  
  
.\boost/filesystem/convenience.hpp(29): note: see declaration of 'boost::filesystem'  
  
libs\log\src\text_file_backend.cpp(919): error C2065: 'directory_iterator': undeclared identifier  
  
libs\log\src\text_file_backend.cpp(919): error C2146: syntax error: missing ';' before identifier 'it'  
  
libs\log\src\text_file_backend.cpp(919): error C3861: 'it': identifier not found  
  
libs\log\src\text_file_backend.cpp(919): error C2563: mismatch in formal parameter list  
  
libs\log\src\text_file_backend.cpp(919): error C2568: ',': unable to resolve function overload  
  
libs\log\src\text_file_backend.cpp(919): note: could be 'range_iterator<const T,void>::type boost::range_adl_barrier::end(const T &)'  
  
libs\log\src\text_file_backend.cpp(919): note: or       'range_iterator<C,void>::type boost::range_adl_barrier::end(T &)'  
  
libs\log\src\text_file_backend.cpp(921): error C2065: 'it': undeclared identifier  
  
libs\log\src\text_file_backend.cpp(921): error C2563: mismatch in formal parameter list  
  
libs\log\src\text_file_backend.cpp(921): error C2568: '!=': unable to resolve function overload  
  
libs\log\src\text_file_backend.cpp(921): note: could be 'range_iterator<const T,void>::type boost::range_adl_barrier::end(const T &)'  
  
libs\log\src\text_file_backend.cpp(921): note: or       'range_iterator<C,void>::type boost::range_adl_barrier::end(T &)'  
  
libs\log\src\text_file_backend.cpp(923): error C2065: 'it': undeclared identifier  
  
libs\log\src\text_file_backend.cpp(925): error C2027: use of undefined type 'boost::filesystem::directory_entry'  
  
.\boost/filesystem/path_traits.hpp(48): note: see declaration of 'boost::filesystem::directory_entry'  
  
libs\log\src\text_file_backend.cpp(926): error C2027: use of undefined type 'boost::filesystem::directory_entry'  
  
.\boost/filesystem/path_traits.hpp(48): note: see declaration of 'boost::filesystem::directory_entry'  
  
libs\log\src\text_file_backend.cpp(1389): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::log::v2s_mt_nt6::sinks::`anonymous-namespace'::filesystem_error'  
  
libs\log\src\text_file_backend.cpp(1392): note: The initializer contains too many elements  
  
libs\log\src\text_file_backend.cpp(1389): error C2672: 'boost::exception_detail::throw_exception_': no matching overloaded function found  
  
...failed compile-c-c++ C:\.conan\727b56\1\boost\bin.v2\libs\log\build\msvc-14.1\rls\adrs-mdl-64\lnk-sttc\thrdp-wn32\thrd-mlt\text_file_backend.obj...  
```
