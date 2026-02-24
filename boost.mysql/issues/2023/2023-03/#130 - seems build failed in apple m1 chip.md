# #130 - seems build failed in apple m1 chip [Closed]

> Username: love1angel  
> Created at: 2023-03-22 10:17:30 UTC  
> Updated at: 2023-04-05 06:49:56 UTC  
> Closed at: 2023-04-05 06:49:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/130  

hi, @anarthal seems apple arm64 chip has compatibility problem  
```  
(base) ➜  mysql git:(master) ✗ cmake -S . -B build  
-- The CXX compiler identification is AppleClang 14.0.0.14000029  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found Boost: /opt/homebrew/lib/cmake/Boost-1.81.0/BoostConfig.cmake (found suitable version "1.81.0", minimum required is "1.80.0") found components: headers system   
-- Looking for C++ include pthread.h  
-- Looking for C++ include pthread.h - found  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE    
-- Found OpenSSL: /opt/homebrew/opt/openssl@3/lib/libcrypto.dylib (found version "3.0.7")    
-- Found Boost: /opt/homebrew/lib/cmake/Boost-1.81.0/BoostConfig.cmake (found suitable version "1.81.0", minimum required is "1.80.0") found components: unit_test_framework   
-- Configuring done  
-- Generating done  
-- Build files have been written to: /Users/helianthusxie/mygithub/mysql/build  
(base) ➜  mysql git:(master) ✗ cmake --build build  
[  1%] Building CXX object CMakeFiles/boost_mysql_asio.dir/tools/asio.cpp.o  
[  3%] Linking CXX static library libboost_mysql_asio.a  
[  3%] Built target boost_mysql_asio  
[  5%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/cmake_pch_arm64.hxx.pch  
[  7%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/channel/read_buffer.cpp.o  
[  9%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/channel/message_parser.cpp.o  
[ 11%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/channel/message_reader.cpp.o  
[ 13%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/channel/message_writer_processor.cpp.o  
[ 15%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/channel/message_writer.cpp.o  
[ 17%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/auth/auth_calculator.cpp.o  
[ 19%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/auxiliar/static_string.cpp.o  
[ 21%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/auxiliar/rows_iterator.cpp.o  
[ 23%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/auxiliar/field_type_traits.cpp.o  
[ 25%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/auxiliar/datetime.cpp.o  
[ 26%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/start_execution_generic.cpp.o  
[ 28%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/start_query.cpp.o  
[ 30%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/start_statement_execution.cpp.o  
[ 32%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/read_some_rows.cpp.o  
[ 34%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/read_all_rows.cpp.o  
[ 36%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/query.cpp.o  
[ 38%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/execute_statement.cpp.o  
[ 40%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/close_statement.cpp.o  
[ 42%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/network_algorithms/ping.cpp.o  
[ 44%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/protocol/capabilities.cpp.o  
[ 46%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/protocol/null_bitmap_traits.cpp.o  
[ 48%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/protocol/deserialize_errc.cpp.o  
[ 50%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/protocol/serialization_test.cpp.o  
[ 51%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/protocol/deserialize_text_field.cpp.o  
[ 53%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/protocol/deserialize_binary_field.cpp.o  
[ 55%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/protocol/deserialize_row.cpp.o  
[ 57%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/detail/protocol/process_error_packet.cpp.o  
[ 59%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/date.cpp.o  
[ 61%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/datetime.cpp.o  
[ 63%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/field_view.cpp.o  
[ 65%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/field.cpp.o  
[ 67%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/row_view.cpp.o  
[ 69%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/row.cpp.o  
[ 71%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/rows_view.cpp.o  
[ 73%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/rows.cpp.o  
[ 75%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/metadata.cpp.o  
[ 76%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/client_errc.cpp.o  
[ 78%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/common_server_errc.cpp.o  
[ 80%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/mysql_server_errc.cpp.o  
[ 82%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/mariadb_server_errc.cpp.o  
[ 84%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/diagnostics.cpp.o  
[ 86%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/statement.cpp.o  
[ 88%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/execution_state.cpp.o  
[ 90%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/results.cpp.o  
[ 92%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/connection.cpp.o  
[ 94%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/regressions.cpp.o  
[ 96%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/throw_on_error.cpp.o  
[ 98%] Building CXX object test/unit/CMakeFiles/boost_mysql_unittests.dir/entry_point.cpp.o  
[100%] Linking CXX executable boost_mysql_unittests  
Undefined symbols for architecture arm64:  
  "boost::asio::any_io_executor::any_io_executor(boost::asio::any_io_executor&&)", referenced from:  
      boost::asio::detail::composed_work_guard<boost::asio::any_io_executor, void>::composed_work_guard(boost::asio::detail::composed_work_guard<boost::asio::any_io_executor, void>&&) in message_reader.cpp.o  
      boost::asio::detail::composed_io_executors<void (boost::asio::any_io_executor)>::composed_io_executors(boost::asio::detail::composed_io_executors<void (boost::asio::any_io_executor)>&&) in message_reader.cpp.o  
      boost::asio::detail::composed_work_guard<boost::asio::any_io_executor, void>::composed_work_guard(boost::asio::detail::composed_work_guard<boost::asio::any_io_executor, void>&&) in message_writer.cpp.o  
      boost::asio::detail::composed_io_executors<void (boost::asio::any_io_executor)>::composed_io_executors(boost::asio::detail::composed_io_executors<void (boost::asio::any_io_executor)>&&) in message_writer.cpp.o  
      boost::asio::detail::composed_work_guard<boost::asio::any_io_executor, void>::composed_work_guard(boost::asio::detail::composed_work_guard<boost::asio::any_io_executor, void>&&) in start_execution_generic.cpp.o  
      boost::asio::detail::composed_io_executors<void (boost::asio::any_io_executor)>::composed_io_executors(boost::asio::detail::composed_io_executors<void (boost::asio::any_io_executor)>&&) in start_execution_generic.cpp.o  
      boost::asio::detail::composed_work_guard<boost::asio::any_io_executor, void>::composed_work_guard(boost::asio::detail::composed_work_guard<boost::asio::any_io_executor, void>&&) in start_query.cpp.o  
      ...  
  "boost::asio::any_io_executor boost::asio::any_io_executor::prefer<boost::asio::execution::detail::outstanding_work::tracked_t<0> >(boost::asio::execution::detail::outstanding_work::tracked_t<0> const&, boost::asio::constraint<traits::prefer_member<boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&>::is_valid, int>::type) const", referenced from:  
      std::__1::enable_if<(call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&)>::overload) == ((boost_asio_prefer_fn::overload_type)3), boost_asio_prefer_fn::call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&), void, void, void, void, void, void, void>::result_type>::type boost_asio_prefer_fn::impl::operator()<boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&>(boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&) const in message_reader.cpp.o  
      std::__1::enable_if<(call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&)>::overload) == ((boost_asio_prefer_fn::overload_type)3), boost_asio_prefer_fn::call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&), void, void, void, void, void, void, void>::result_type>::type boost_asio_prefer_fn::impl::operator()<boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&>(boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&) const in message_writer.cpp.o  
      std::__1::enable_if<(call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&)>::overload) == ((boost_asio_prefer_fn::overload_type)3), boost_asio_prefer_fn::call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&), void, void, void, void, void, void, void>::result_type>::type boost_asio_prefer_fn::impl::operator()<boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&>(boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&) const in start_execution_generic.cpp.o  
      std::__1::enable_if<(call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&)>::overload) == ((boost_asio_prefer_fn::overload_type)3), boost_asio_prefer_fn::call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&), void, void, void, void, void, void, void>::result_type>::type boost_asio_prefer_fn::impl::operator()<boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&>(boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&) const in start_query.cpp.o  
      std::__1::enable_if<(call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&)>::overload) == ((boost_asio_prefer_fn::overload_type)3), boost_asio_prefer_fn::call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&), void, void, void, void, void, void, void>::result_type>::type boost_asio_prefer_fn::impl::operator()<boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&>(boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&) const in start_statement_execution.cpp.o  
      std::__1::enable_if<(call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&)>::overload) == ((boost_asio_prefer_fn::overload_type)3), boost_asio_prefer_fn::call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&), void, void, void, void, void, void, void>::result_type>::type boost_asio_prefer_fn::impl::operator()<boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&>(boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&) const in read_some_rows.cpp.o  
      std::__1::enable_if<(call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&)>::overload) == ((boost_asio_prefer_fn::overload_type)3), boost_asio_prefer_fn::call_traits<boost_asio_prefer_fn::impl, boost::asio::any_io_executor const&, void (boost::asio::execution::detail::outstanding_work::tracked_t<0> const&), void, void, void, void, void, void, void>::result_type>::type boost_asio_prefer_fn::impl::operator()<boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&>(boost::asio::any_io_executor const&, boost::asio::execution::detail::outstanding_work::tracked_t<0> const&) const in read_all_rows.cpp.o  
      ...  
ld: symbol(s) not found for architecture arm64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
make[2]: *** [test/unit/boost_mysql_unittests] Error 1  
make[1]: *** [test/unit/CMakeFiles/boost_mysql_unittests.dir/all] Error 2  
make: *** [all] Error 2  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2023-03-22 14:13:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/130#issuecomment-1479640871  

Investigating - I'm in the process of adding an ARM64 CI

---

## Comment 2

> Username: nav-mohan  
> Created at: 2023-03-25 05:55:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/130#issuecomment-1483735482  

I had the same problem. It was because CMake wasn't using C++11  
`set (CMAKE_CXX_STANDARD 11)` fixed the issue for me.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-03-25 20:35:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/130#issuecomment-1483916506  

You shouldn't need to specify `set(CMAKE_CXX_STANDARD 11)` - the helper library `boost_mysql_asio` is probably missing a `target_compile_features(boost_mysql INTERFACE cxx_std_11)`. Will try to reproduce. Raised #134 to set up an ARM CI in any case.
