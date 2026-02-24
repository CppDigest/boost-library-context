# #207 - Cannot build a boost::redis example using CMake [Closed]

> Username: hungptit  
> Created at: 2024-08-11 14:37:59 UTC  
> Updated at: 2024-10-08 12:43:20 UTC  
> Closed at: 2024-10-08 12:43:20 UTC  
> Url: https://github.com/boostorg/redis/issues/207  

I use this CMake config to build boost::redis and other boost libraries  
  
```  
include(FetchContent)  
  
set(BOOST_INCLUDE_LIBRARIES redis mysql asio json)  
set(BOOST_ENABLE_CMAKE ON)  
  
include(FetchContent)  
FetchContent_Declare(  
  Boost  
  GIT_REPOSITORY https://github.com/boostorg/boost.git  
  GIT_TAG boost-1.85.0  
  GIT_SHALLOW TRUE)  
  
FetchContent_MakeAvailable(Boost)  
```  
  
But  I could not compile [the provided example](https://github.com/boostorg/redis/blob/develop/example/cpp20_intro.cpp) because of these linker errors.  
  
```  
[ 97%] Linking CXX executable boost_redis_test  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: CMakeFiles/boost_redis_test.dir/boost_redis_test.cpp.o: in function `boost::redis::adapter::throw_exception_from_error(boost::redis::adapter::error const&, boost::source_location const&) [clone .isra.0]':  
boost_redis_test.cpp:(.text+0x1277): undefined reference to `boost::redis::make_error_code(boost::redis::error)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: boost_redis_test.cpp:(.text+0x12c0): undefined reference to `boost::redis::make_error_code(boost::redis::error)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: boost_redis_test.cpp:(.text+0x12e5): undefined reference to `boost::redis::make_error_code(boost::redis::error)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: CMakeFiles/boost_redis_test.dir/boost_redis_test.cpp.o: in function `co_main(co_main(boost::redis::config)::_Z7co_mainN5boost5redis6configE.Frame*) [clone .actor]':  
boost_redis_test.cpp:(.text+0x1c08): undefined reference to `boost::redis::connection::connection(boost::asio::any_io_executor, boost::asio::ssl::context, unsigned long)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: boost_redis_test.cpp:(.text+0x1ed5): undefined reference to `boost::redis::connection::async_run_impl(boost::redis::config const&, boost::redis::logger, boost::asio::any_completion_handler<void (boost::system::error_code)>)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: boost_redis_test.cpp:(.text+0x1f55): undefined reference to `boost::redis::resp3::add_header(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, boost::redis::resp3::type, unsigned long)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: boost_redis_test.cpp:(.text+0x1f73): undefined reference to `boost::redis::resp3::boost_redis_to_bulk(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::basic_string_view<char, std::char_traits<char> >)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: boost_redis_test.cpp:(.text+0x1f87): undefined reference to `boost::redis::resp3::boost_redis_to_bulk(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::basic_string_view<char, std::char_traits<char> >)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: boost_redis_test.cpp:(.text+0x1f9c): undefined reference to `boost::redis::detail::has_response(std::basic_string_view<char, std::char_traits<char> >)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: boost_redis_test.cpp:(.text+0x2139): undefined reference to `boost::redis::connection::cancel(boost::redis::operation)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: CMakeFiles/boost_redis_test.dir/boost_redis_test.cpp.o: in function `void boost::redis::detail::exec_op<boost::redis::detail::connection_base<boost::asio::any_io_executor> >::operator()<boost::asio::detail::composed_op<boost::redis::detail::exec_op<boost::redis::detail::connection_base<boost::asio::any_io_executor> >, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::awaitable_async_op_handler<void (boost::system::error_code, unsigned long), boost::asio::any_io_executor>, void (boost::system::error_code, unsigned long)> >(boost::asio::detail::composed_op<boost::redis::detail::exec_op<boost::redis::detail::connection_base<boost::asio::any_io_executor> >, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::awaitable_async_op_handler<void (boost::system::error_code, unsigned long), boost::asio::any_io_executor>, void (boost::system::error_code, unsigned long)>&, boost::system::error_code, unsigned long) [clone .isra.0]':  
boost_redis_test.cpp:(.text+0x361d): undefined reference to `boost::redis::make_error_code(boost::redis::error)'  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: CMakeFiles/boost_redis_test.dir/boost_redis_test.cpp.o: in function `std::_Function_handler<void (unsigned long, boost::redis::resp3::basic_node<std::basic_string_view<char, std::char_traits<char> > > const&, boost::system::error_code&), boost::redis::adapter::detail::static_adapter<std::tuple<boost::system::result<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::redis::adapter::error> > > >::_M_invoke(std::_Any_data const&, unsigned long&&, boost::redis::resp3::basic_node<std::basic_string_view<char, std::char_traits<char> > > const&, boost::system::error_code&)':  
boost_redis_test.cpp:(.text._ZNSt17_Function_handlerIFvmRKN5boost5redis5resp310basic_nodeISt17basic_string_viewIcSt11char_traitsIcEEEERNS0_6system10error_codeEENS1_7adapter6detail14static_adapterISt5tupleIJNSB_6resultINSt7__cxx1112basic_stringIcS6_SaIcEEENSF_5errorEEEEEEEE9_M_invokeERKSt9_Any_dataOmSA_SD_[_ZNSt17_Function_handlerIFvmRKN5boost5redis5resp310basic_nodeISt17basic_string_viewIcSt11char_traitsIcEEEERNS0_6system10error_codeEENS1_7adapter6detail14static_adapterISt5tupleIJNSB_6resultINSt7__cxx1112basic_stringIcS6_SaIcEEENSF_5errorEEEEEEEE9_M_invokeERKSt9_Any_dataOmSA_SD_]+0x1e9): undefined reference to `boost::redis::make_error_code(boost::redis::error)'  
collect2: error: ld returned 1 exit status  
gmake[2]: *** [boost/CMakeFiles/boost_redis_test.dir/build.make:107: boost/boost_redis_test] Error 1  
gmake[1]: *** [CMakeFiles/Makefile2:1895: boost/CMakeFiles/boost_redis_test.dir/all] Error 2  
```  
  
My configuration works fine with boost::json. What do I miss?

---

## Comment 1

> Username: D0zee  
> Created at: 2024-10-02 10:58:00 UTC  
> Url: https://github.com/boostorg/redis/issues/207#issuecomment-2388362569  

@hungptit Had the same issue. Try to add `#include <boost/redis/src.hpp>`. It works for me.

---

## Comment 2

> Username: hungptit  
> Created at: 2024-10-08 12:43:14 UTC  
> Url: https://github.com/boostorg/redis/issues/207#issuecomment-2399742632  

This hack works. Thank you.
