# #21 - Compilation failures caused by Boost.Asio [Closed]

> Username: Lastique  
> Created at: 2017-10-24 20:32:42 UTC  
> Updated at: 2017-12-19 20:30:41 UTC  
> Closed at: 2017-12-19 20:30:41 UTC  
> Url: https://github.com/boostorg/process/issues/21  

I'm having test compilation failures on Windows on the develop branch:  
  
```  
multi_ref1.cpp  
..\..\..\boost/asio/io_service.hpp(27): error C2371: 'boost::asio::io_service': redefinition; different basic types  
..\..\..\boost/process/detail/traits/async.hpp(15): note: see declaration of 'boost::asio::io_service'  
..\..\..\boost/process/detail/windows/io_service_ref.hpp(133): error C2664: 'boost::asio::windows::object_handle::object_handle(const boost::asio::windows::object_handle &)': cannot convert argument 1 from 'boost::asio::io_service' to 'boost::asio::io_context &'  
..\..\..\boost/process/detail/windows/io_service_ref.hpp(145): error C2039: 'native': is not a member of 'boost::asio::windows::object_handle'  
..\..\..\boost/asio/windows/object_handle.hpp(61): note: see declaration of 'boost::asio::windows::object_handle'  
..\..\..\boost/process/detail/windows/asio_fwd.hpp(42): error C2371: 'boost::asio::windows::object_handle': redefinition; different basic types  
..\..\..\boost/asio/windows/object_handle.hpp(61): note: see declaration of 'boost::asio::windows::object_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(50): error C2371: 'boost::asio::windows::stream_handle': redefinition; different basic types  
..\..\..\boost/process/detail/windows/asio_fwd.hpp(34): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(146): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(147): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(179): error C2664: 'boost::asio::windows::stream_handle::stream_handle(const boost::asio::windows::stream_handle &)': cannot convert argument 1 from 'boost::asio::io_service' to 'boost::asio::io_context &'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(185): error C2664: 'boost::asio::windows::stream_handle::stream_handle(const boost::asio::windows::stream_handle &)': cannot convert argument 1 from 'boost::asio::io_service' to 'boost::asio::io_context &'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(195): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(196): error C3536: 'source_in': cannot be used before it is initialized  
..\..\..\boost/process/detail/windows/async_pipe.hpp(196): error C2446: '==': no conversion from 'const boost::winapi::HANDLE_' to 'int'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(196): note: There is no context in which this conversion is possible  
..\..\..\boost/process/detail/windows/async_pipe.hpp(201): error C2664: 'BOOL DuplicateHandle(HANDLE,HANDLE,HANDLE,LPHANDLE,DWORD,BOOL,DWORD)': cannot convert argument 2 from 'int' to 'HANDLE'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(201): note: Conversion from integral type to pointer type requires reinterpret_cast, C-style cast or function-style cast  
..\..\..\boost/process/detail/windows/async_pipe.hpp(204): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(204): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
..\..\..\boost/process/detail/windows/async_pipe.hpp(211): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(212): error C3536: 'sink_in': cannot be used before it is initialized  
..\..\..\boost/process/detail/windows/async_pipe.hpp(212): error C2446: '==': no conversion from 'const boost::winapi::HANDLE_' to 'int'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(212): note: There is no context in which this conversion is possible  
..\..\..\boost/process/detail/windows/async_pipe.hpp(217): error C2664: 'BOOL DuplicateHandle(HANDLE,HANDLE,HANDLE,LPHANDLE,DWORD,BOOL,DWORD)': cannot convert argument 2 from 'int' to 'HANDLE'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(217): note: Conversion from integral type to pointer type requires reinterpret_cast, C-style cast or function-style cast  
..\..\..\boost/process/detail/windows/async_pipe.hpp(220): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(220): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
..\..\..\boost/process/detail/windows/async_pipe.hpp(236): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(237): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(239): error C3536: 'source_in': cannot be used before it is initialized  
..\..\..\boost/process/detail/windows/async_pipe.hpp(239): error C2446: '==': no conversion from 'const boost::winapi::HANDLE_' to 'int'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(239): note: There is no context in which this conversion is possible  
..\..\..\boost/process/detail/windows/async_pipe.hpp(244): error C2664: 'BOOL DuplicateHandle(HANDLE,HANDLE,HANDLE,LPHANDLE,DWORD,BOOL,DWORD)': cannot convert argument 2 from 'int' to 'HANDLE'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(244): note: Conversion from integral type to pointer type requires reinterpret_cast, C-style cast or function-style cast  
..\..\..\boost/process/detail/windows/async_pipe.hpp(247): error C3536: 'sink_in': cannot be used before it is initialized  
..\..\..\boost/process/detail/windows/async_pipe.hpp(247): error C2446: '==': no conversion from 'const boost::winapi::HANDLE_' to 'int'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(247): note: There is no context in which this conversion is possible  
..\..\..\boost/process/detail/windows/async_pipe.hpp(252): error C2664: 'BOOL DuplicateHandle(HANDLE,HANDLE,HANDLE,LPHANDLE,DWORD,BOOL,DWORD)': cannot convert argument 2 from 'int' to 'HANDLE'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(252): note: Conversion from integral type to pointer type requires reinterpret_cast, C-style cast or function-style cast  
..\..\..\boost/process/detail/windows/async_pipe.hpp(263): error C2664: 'boost::asio::windows::stream_handle::stream_handle(const boost::asio::windows::stream_handle &)': cannot convert argument 1 from 'boost::asio::io_service' to 'boost::asio::io_context &'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(302): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(305): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(307): error C2660: 'DuplicateHandle': function does not take 6 arguments  
..\..\..\boost/process/detail/windows/async_pipe.hpp(310): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(313): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(315): error C2660: 'DuplicateHandle': function does not take 6 arguments  
..\..\..\boost/process/detail/windows/async_pipe.hpp(328): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/detail/windows/async_pipe.hpp(331): error C2039: 'native': is not a member of 'boost::asio::windows::stream_handle'  
..\..\..\boost/asio/windows/stream_handle.hpp(51): note: see declaration of 'boost::asio::windows::stream_handle'  
..\..\..\boost/process/async_system.hpp(44): error C2143: syntax error: missing ';' before '<'  
..\..\..\boost/process/async_system.hpp(93): note: see reference to class template instantiation 'boost::process::detail::async_system_handler<ExitHandler>' being compiled  
..\..\..\boost/process/async_system.hpp(44): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
..\..\..\boost/process/async_system.hpp(45): error C2238: unexpected token(s) preceding ';'  
  
    call "C:\Users\Lastique\AppData\Local\Temp\b2_msvc_14.1_vcvars32_.cmd" >nul  
cl /Zm800 -nologo @"..\..\..\bin.v2\libs\process\test\msvc-14.1\release\threadapi-win32\threading-multi\multi_ref1.obj.rsp"   
```  
  
It looks like at least initially the errors are caused by recent changes in Boost.Asio (e.g. `boost::asio::io_service` is no longer a class).

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2017-10-24 20:34:21 UTC  
> Url: https://github.com/boostorg/process/issues/21#issuecomment-339122455  

Oh ok, wasn't sure that changed already, let me just merge #19 ...done.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2017-10-24 21:16:35 UTC  
> Url: https://github.com/boostorg/process/issues/21#issuecomment-339134348  

was a bit to quick, tests are cycling: https://ci.appveyor.com/project/klemens-morgenstern/boost-process

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2017-12-19 20:30:41 UTC  
> Url: https://github.com/boostorg/process/issues/21#issuecomment-352877609  

I think it works now.
