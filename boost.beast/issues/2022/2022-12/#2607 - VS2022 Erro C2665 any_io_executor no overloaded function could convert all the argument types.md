# #2607 - VS2022 Erro C2665 any_io_executor no overloaded function could convert all the argument types [Closed]

> Username: rflsouza  
> Created at: 2022-12-28 13:22:27 UTC  
> Updated at: 2023-08-18 20:56:25 UTC  
> Closed at: 2023-08-18 20:56:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2607  

### Steps necessary to reproduce the problem  
  
When I use **boost version 1.74** ( _BOOST_BEAST_VERSION 300_ ) I have no problem.  
  
When I use **boost version 1.81** ( _BOOST_BEAST_VERSION 345_ )  the error occurs when compiling my code.  
  
in both I am using /permissive option with C++20.  
  
I'm using code similar to the project [CppCon2018 ](https://github.com/vinniefalco/CppCon2018)  
  
**Erro**:  
```  
C:\boost_1_81_0\boost\asio\detail\io_object_impl.hpp(59,16): error C2665: 'boost::asio::any_io_executor::any_io_executor': no overloaded function could convert all the argument types (compiling source file listener.cpp)  
  
C:\boost_1_81_0\boost\asio\impl\any_io_executor.ipp(34,18): message : could be 'boost::asio::any_io_executor::any_io_executor(std::nullptr_t) noexcept' (compiling source file listener.cpp)  
  
C:\boost_1_81_0\boost\asio\detail\io_object_impl.hpp(59,16): message : 'boost::asio::any_io_executor::any_io_executor(std::nullptr_t) noexcept': cannot convert argument 1 from 'boost::asio::io_context::executor_type' to 'std::nullptr_t' (compiling source file listener.cpp)  
  
C:\boost_1_81_0\boost\asio\detail\io_object_impl.hpp(59,37): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called (compiling source file listener.cpp)  
  
C:\boost_1_81_0\boost\asio\any_io_executor.hpp(189,3): message : or       'boost::asio::any_io_executor::any_io_executor(std::nothrow_t,Executor,constraint<std::conditional<!std::is_same<OtherAnyExecutor,boost::asio::any_io_executor>::value&&!std::is_base_of<boost::asio::execution::detail::any_executor_base,Executor>::value,boost::asio::execution::detail::is_valid_target_executor<Executor,boost::asio::any_io_executor::supportable_properties_type>,std::false_type>::type::value,int>::type) noexcept' (compiling source file listener.cpp)  
```  
  
### All relevant compiler information  
**SO**: Windows 11 Pro  
**VS**: Microsoft Visual Studio Professional 2022 (64-bit) - Version 17.4.3  
**Compiler**:  
C:\...\MSVC\14.34.31933\bin\HostX64\x64\CL.exe /c  
 /IC:\projetos\libs\boost_1_81_0   
 /Zi /nologo /W3 /WX- /diagnostics:column /sdl /MP /Od /Ob1 /Ot /Oy-   
 /D _DEBUG /D _CONSOLE /D _WIN32_WINNT=0x0601  
 /GF /EHa /RTC1 /MTd /GS /guard:cf /Gy /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /GR /std:c++20 /Fo"x64\D_LocalTime\\" /Fd"x64\D_LocalTime\vc143.pdb" /external:W3 /Gd /TP /FC /errorReport:prompt /bigobj /permissive -DPSAPI_VERSION=1 listener.cpp webserver.cpp

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-12-28 23:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2607#issuecomment-1366970008  

This looks like an asio issue. in order to help I would need to see some code.

---

## Comment 2

> Username: rflsouza  
> Created at: 2022-12-29 12:44:16 UTC  
> Updated at: 2022-12-30 12:23:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2607#issuecomment-1367294374  

@klemens-morgenstern Thanks a lot for the help!  
  
The problem occurs in the example that **Vinnie** made [CppCon2018](https://github.com/vinniefalco/CppCon2018).  
  
Just make the **clone** and try to run with the command below that will show the error.  
  
`  
CL.exe /c /IC:\boost_1_81_0 /Zi /nologo /W3 /WX- /diagnostics:column /sdl /MP /Od /Ob1 /Ot /Oy- /D _DEBUG /D _CONSOLE /D _WIN32_WINNT=0x0601 /GF /EHa /RTC1 /MTd /GS /guard:cf /Gy /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /GR /std:c++20 /Fo"x64\D_LocalTime\\" /external:W3 /Gd /TP /FC /errorReport:prompt /bigobj /permissive listener.cpp main.cpp  
`  
  
**Running on the command line:**  
```  
C:\CppCon2018>CL.exe /c /IC:\boost_1_81_0 /Zi /nologo /W3 /WX- /diagnostics:column /sdl /MP /Od /Ob1 /Ot /Oy- /D _DEBUG /D _CONSOLE /D _WIN32_WINNT=0x0601 /GF /EHa /RTC1 /MTd /GS /guard:cf /Gy /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /GR /std:c++20 /Fo"x64\D_LocalTime\\" /external:W3 /Gd /TP /FC /errorReport:prompt /bigobj /permissive listener.cpp main.cpp  
listener.cpp  
main.cpp  
C:\boost_1_81_0\boost\asio\detail\io_object_impl.hpp(59,16): error C2665: 'boost::asio::any_io_executor::any_io_executor': no overloaded function could convert all the argument types (compiling source file main.cpp)  
C:\boost_1_81_0\boost\asio\impl\any_io_executor.ipp(34,18): note: could be 'boost::asio::any_io_executor::any_io_executor(std::nullptr_t) noexcept' (compiling source file main.cpp)  
C:\boost_1_81_0\boost\asio\detail\io_object_impl.hpp(59,16): note: 'boost::asio::any_io_executor::any_io_executor(std::nullptr_t) noexcept': cannot convert argument 1 from 'boost::asio::io_context::executor_type' to 'std::nullptr_t' (compiling source file main.cpp)  
C:\boost_1_81_0\boost\asio\detail\io_object_impl.hpp(59,37): note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called (compiling source file main.cpp)  
C:\boost_1_81_0\boost\asio\any_io_executor.hpp(189,3): note: or       'boost::asio::any_io_executor::any_io_executor(std::nothrow_t,Executor,constraint<std::conditional<!std::is_same<OtherAnyExecutor,boost::asio::any_io_executor>::value&&!std::is_base_of<boost::asio::execution::detail::any_executor_base,Executor>::value,boost::asio::execution::detail::is_valid_target_executor<Executor,boost::asio::any_io_executor::supportable_properties_type>,std::false_type>::type::value,int>::type) noexcept' (compiling source file main.cpp)  
```

---

## Comment 3

> Username: WolfJMZ  
> Created at: 2023-01-09 01:52:44 UTC  
> Updated at: 2023-01-09 01:55:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2607#issuecomment-1375012767  

Even the barebones async example from the boost beast examples fails to compile with this compile failure message. I'm on boost 1.80 and vs2022 using c++20.  
  
This gist is taken copy/paste from the example with the only change being to replace the io_context with the any_io_executor.  
  
https://gist.github.com/jammerxd/c31df3ba0a20675eec0179d20d6eaa5d  
  
Edit:  
  
The example also fails with using the thread_pool::executor_type.

---

## Comment 4

> Username: WolfJMZ  
> Created at: 2023-01-09 02:53:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2607#issuecomment-1375042584  

Actually - I found there's no issue - you just need to be extremely careful with your types. I've updated my gist with a working version.

---

## Comment 5

> Username: WolfJMZ  
> Created at: 2023-01-09 03:38:37 UTC  
> Updated at: 2023-01-09 03:39:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2607#issuecomment-1375068385  

Additionally the other issue I had was that I was instantiating my server by calling the constructor - instead of directly passing parameters through the `make_shared` method:  
  
WRONG/CAUSES COMPILE ERRORS:  
`std::make_shared<listener>(listener(io_context->get_executor(), tcp::endpoint{address, port}))`  
  
ALSO WRONG/CAUSES COMPILE ERRORS:  
`std::make_shared<listener>(new listener(io_context->get_executor(), tcp::endpoint{address, port}))`  
  
CORRECT:  
`std::make_shared<listener>(io_context->get_executor(), tcp::endpoint{address, port})`  
  
Notice in the correct version that we do not call the `listener` constructor directly.

---

## Comment 6

> Username: ashtum  
> Created at: 2023-08-18 13:39:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2607#issuecomment-1683937265  

@rflsouza Is this still open?

---

## Comment 7

> Username: rflsouza  
> Created at: 2023-08-18 20:56:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2607#issuecomment-1684427919  

@ashtum   
  
Unfortunately I didn't have time to try the migration again, if you want you can close the case! in the future when I migrate I try to make the changes  
  
thanks.
