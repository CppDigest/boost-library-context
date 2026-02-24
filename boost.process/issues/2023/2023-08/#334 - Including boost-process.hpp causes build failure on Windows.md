# #334 - Including boost/process.hpp causes build failure on Windows [Closed]

> Username: Pentarctagon  
> Created at: 2023-08-12 03:46:56 UTC  
> Updated at: 2023-08-16 18:26:05 UTC  
> Closed at: 2023-08-16 18:26:05 UTC  
> Url: https://github.com/boostorg/process/issues/334  

When including boost/process.hpp and then using `boost::process::child` to start a process, on Windows it results in a bunch of compile errors:  
```  
2023-08-09T20:26:09.7125010Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/locale.hpp(81,37): error C2039: 'WC_NO_BEST_FIT_CHARS_': is not a member of 'boost::winapi' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7126121Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/locale.hpp(81,1): error C2065: 'WC_NO_BEST_FIT_CHARS_': undeclared identifier [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7127361Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(41,43): error C2079: 'boost::process::detail::windows::async_pipe::_source' uses undefined class 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7129994Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(42,43): error C2079: 'boost::process::detail::windows::async_pipe::_sink' uses undefined class 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7131306Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(51,35): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7132841Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(51,49): error C3646: 'executor_type': unknown override specifier [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7134437Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(51,62): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7135582Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(111,32): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7137157Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(116,34): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7138788Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(124,32): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7140151Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(129,34): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7141285Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(140,26): error C2672: 'boost::asio::post': no matching overloaded function found [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7142418Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(140,1): error C2780: 'auto boost::asio::post(NullaryToken &&)': expects 1 arguments - 2 provided [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7143534Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(142,26): error C2672: 'boost::asio::post': no matching overloaded function found [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7144652Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(142,1): error C2780: 'auto boost::asio::post(NullaryToken &&)': expects 1 arguments - 2 provided [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7145790Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(168,46): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::asio::windows::stream_handle &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7146950Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(169,46): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::asio::windows::stream_handle &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7148277Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(193,42): error C2440: 'return': cannot convert from 'const int' to 'const boost::process::detail::windows::async_pipe::handle_type &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7183786Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(194,42): error C2440: 'return': cannot convert from 'const int' to 'const boost::process::detail::windows::async_pipe::handle_type &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7185501Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(196,32): error C2440: 'return': cannot convert from 'int' to 'boost::process::detail::windows::async_pipe::handle_type &&' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7191059Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(197,32): error C2440: 'return': cannot convert from 'int' to 'boost::process::detail::windows::async_pipe::handle_type &&' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7192338Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(200,5): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7193539Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(199,17): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7194729Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(201,47): error C2079: 'stolen' uses undefined class 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7196247Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(201,1): error C2440: 'initializing': cannot convert from 'boost::asio::io_context::executor_type' to 'int' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7197419Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(207,5): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7198566Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(206,17): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7199751Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(208,47): error C2079: 'stolen' uses undefined class 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7200938Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(208,1): error C2440: 'initializing': cannot convert from 'boost::asio::io_context::executor_type' to 'int' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7202117Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(215,5): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7203295Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(214,17): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7204641Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(219,1): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::process::detail::windows::async_pipe::handle_type &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7205779Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(220,1): error C3536: 'source_in': cannot be used before it is initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7206857Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(220,1): error C2446: '==': no conversion from 'const boost::winapi::HANDLE_' to 'int' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7208114Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(222,36): error C2664: 'BOOL DuplicateHandle(HANDLE,HANDLE,HANDLE,LPHANDLE,DWORD,BOOL,DWORD)': cannot convert argument 2 from 'int' to 'HANDLE' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7209750Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(228,53): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7210923Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(231,5): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7212085Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(230,17): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7213407Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(235,1): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::process::detail::windows::async_pipe::handle_type &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7214519Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(236,1): error C3536: 'sink_in': cannot be used before it is initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7215600Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(236,1): error C2446: '==': no conversion from 'const boost::winapi::HANDLE_' to 'int' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7216846Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(238,36): error C2664: 'BOOL DuplicateHandle(HANDLE,HANDLE,HANDLE,LPHANDLE,DWORD,BOOL,DWORD)': cannot convert argument 2 from 'int' to 'HANDLE' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7218338Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(244,53): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7219684Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(249,47): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::process::detail::windows::async_pipe::handle_type &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7220903Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(250,45): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::process::detail::windows::async_pipe::handle_type &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7222428Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(259,56): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::process::detail::windows::async_pipe::handle_type &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7223633Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(260,54): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::process::detail::windows::async_pipe::handle_type &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7224797Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(262,8): error C3536: 'source_in': cannot be used before it is initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7225848Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(262,60): error C2446: '==': no conversion from 'const boost::winapi::HANDLE_' to 'int' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7227093Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(264,32): error C2664: 'BOOL DuplicateHandle(HANDLE,HANDLE,HANDLE,LPHANDLE,DWORD,BOOL,DWORD)': cannot convert argument 2 from 'int' to 'HANDLE' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7228308Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(270,8): error C3536: 'sink_in': cannot be used before it is initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7229363Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(270,60): error C2446: '==': no conversion from 'const boost::winapi::HANDLE_' to 'int' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7230737Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(272,32): error C2664: 'BOOL DuplicateHandle(HANDLE,HANDLE,HANDLE,LPHANDLE,DWORD,BOOL,DWORD)': cannot convert argument 2 from 'int' to 'HANDLE' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7231993Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(287,74): error C2440: 'initializing': cannot convert from 'boost::asio::io_context' to 'int' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7233137Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(288,1): error C2439: 'boost::process::detail::windows::async_pipe::_source': member could not be initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7234283Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(287,93): error C2440: 'initializing': cannot convert from 'boost::asio::io_context' to 'int' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7235365Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(288,1): error C2439: 'boost::process::detail::windows::async_pipe::_sink': member could not be initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7236514Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(370,84): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::asio::windows::stream_handle &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7237589Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(370,21): error C2530: 'source_in': references must be initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7238796Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(371,82): error C2440: 'const_cast': cannot convert from 'const int' to 'boost::asio::windows::stream_handle &' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7239869Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(371,21): error C2530: 'sink_in': references must be initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7240890Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(373,14): error C3536: 'source_in': cannot be used before it is initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7241952Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(377,32): error C2660: 'DuplicateHandle': function does not take 6 arguments [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7242964Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(383,8): error C3536: 'sink_in': cannot be used before it is initialized [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7244016Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(385,32): error C2660: 'DuplicateHandle': function does not take 6 arguments [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7245155Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(393,56): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7246432Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(395,56): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7247622Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(398,56): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
2023-08-09T20:26:09.7248834Z   D:\a\wesnoth\wesnoth\vcpkg_installed\x64-windows\include\boost/process/detail/windows/async_pipe.hpp(400,56): error C2027: use of undefined type 'boost::asio::windows::basic_stream_handle<boost::asio::any_io_executor>' [D:\a\wesnoth\wesnoth\src\wesnoth-client.vcxproj]  
```  
  
How can these errors be resolved so that compilation works? I haven't been able to find anything particularly useful rergarding why all these error would be occurring or what they really mean.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-14 09:38:22 UTC  
> Url: https://github.com/boostorg/process/issues/334#issuecomment-1677006283  

What are you compiling with? msvc? mingw? cygwin?

---

## Comment 2

> Username: Pentarctagon  
> Created at: 2023-08-14 14:00:44 UTC  
> Url: https://github.com/boostorg/process/issues/334#issuecomment-1677371975  

msvc

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-15 03:49:35 UTC  
> Url: https://github.com/boostorg/process/issues/334#issuecomment-1678389335  

Which version?

---

## Comment 4

> Username: Pentarctagon  
> Created at: 2023-08-15 14:08:06 UTC  
> Url: https://github.com/boostorg/process/issues/334#issuecomment-1678995782  

16.11.33920.266 - https://github.com/actions/runner-images/blob/main/images/win/Windows2019-Readme.md#visual-studio-enterprise-2019

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-08-16 00:20:32 UTC  
> Url: https://github.com/boostorg/process/issues/334#issuecomment-1679792783  

Ok, this is super odd. I am using the right include. What windows version are you building for?  
  
```cpp  
#if BOOST_USE_WINAPI_VERSION >= BOOST_WINAPI_VERSION_WIN2K  
BOOST_CONSTEXPR_OR_CONST DWORD_ WC_NO_BEST_FIT_CHARS_ = 0x00000400;  
#endif  
```  
  
There seems to be an exclusion for windows pre 2k. That's the only thing I can come up with.

---

## Comment 6

> Username: Pentarctagon  
> Created at: 2023-08-16 00:45:22 UTC  
> Url: https://github.com/boostorg/process/issues/334#issuecomment-1679808497  

I'm not sure - we don't set it anywhere ourselves and all dependencies are built and provided via vcpkg.  
  
We do set `_WIN32_WINNT=_WIN32_WINNT_WIN7` for our own code though.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2023-08-16 01:41:43 UTC  
> Url: https://github.com/boostorg/process/issues/334#issuecomment-1679843527  

That is odd. Can you try to define `BOOST_USE_WINAPI_VERSION` as `_WIN32_WINNT_WIN7`  
  
Is `_WIN32_WINNT_WIN7` actually defined as an int in your build?

---

## Comment 8

> Username: Pentarctagon  
> Created at: 2023-08-16 18:26:05 UTC  
> Url: https://github.com/boostorg/process/issues/334#issuecomment-1681085138  

It turns out it's not. Using `_WIN32_WINNT=0x0601` instead does result in the build being successful though. Thanks!
