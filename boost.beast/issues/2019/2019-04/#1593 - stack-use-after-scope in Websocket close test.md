# #1593 - stack-use-after-scope in Websocket close test [Closed]

> Username: djarek  
> Created at: 2019-04-25 21:16:50 UTC  
> Updated at: 2019-05-04 16:04:53 UTC  
> Closed at: 2019-05-04 16:04:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1593  

```==3405==ERROR: AddressSanitizer: stack-use-after-scope on address 0x7ffd687274b0 at pc 0x0000009f51aa bp 0x7ffd68721d00 sp 0x7ffd68721cf8  
READ of size 8 at 0x7ffd687274b0 thread T0  
    #0 0x9f51a9 in boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type::begin() const (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x9f51a9)  
    #1 0x9f9be2 in boost::beast::buffers_suffix<boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type>::buffers_suffix(boost::beast::buffers_suffix<boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type> const&) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x9f9be2)  
    #2 0xabb73f in boost::beast::websocket::stream<boost::beast::test::stream, true>::read_some_op<boost::beast::websocket::stream<boost::beast::test::stream, true>::read_op<boost::beast::websocket::close_test::testSuspend()::'lambda4'(boost::beast::test::fail_count&)::operator()(boost::beast::test::fail_count&) const::'lambda0'(boost::system::error_code, unsigned long), boost::beast::basic_multi_buffer<std::__1::allocator<char> > >, boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type>::read_some_op(boost::beast::websocket::stream<boost::beast::test::stream, true>::read_some_op<boost::beast::websocket::stream<boost::beast::test::stream, true>::read_op<boost::beast::websocket::close_test::testSuspend()::'lambda4'(boost::beast::test::fail_count&)::operator()(boost::beast::test::fail_count&) const::'lambda0'(boost::system::error_code, unsigned long), boost::beast::basic_multi_buffer<std::__1::allocator<char> > >, boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type>&&) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0xabb73f)  
    #3 0xac0740 in boost::beast::test::stream::read_op<boost::beast::websocket::stream<boost::beast::test::stream, true>::read_some_op<boost::beast::websocket::stream<boost::beast::test::stream, true>::read_op<boost::beast::websocket::close_test::testSuspend()::'lambda4'(boost::beast::test::fail_count&)::operator()(boost::beast::test::fail_count&) const::'lambda0'(boost::system::error_code, unsigned long), boost::beast::basic_multi_buffer<std::__1::allocator<char> > >, boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type>, boost::beast::detail::buffers_pair<true> >::lambda::lambda(boost::beast::test::stream::read_op<boost::beast::websocket::stream<boost::beast::test::stream, true>::read_some_op<boost::beast::websocket::stream<boost::beast::test::stream, true>::read_op<boost::beast::websocket::close_test::testSuspend()::'lambda4'(boost::beast::test::fail_count&)::operator()(boost::beast::test::fail_count&) const::'lambda0'(boost::system::error_code, unsigned long), boost::beast::basic_multi_buffer<std::__1::allocator<char> > >, boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type>, boost::beast::detail::buffers_pair<true> >::lambda&&) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0xac0740)  
    #4 0xabe69e in boost::asio::detail::executor_op<boost::beast::detail::bind_front_wrapper<boost::beast::test::stream::read_op<boost::beast::websocket::stream<boost::beast::test::stream, true>::read_some_op<boost::beast::websocket::stream<boost::beast::test::stream, true>::read_op<boost::beast::websocket::close_test::testSuspend()::'lambda4'(boost::beast::test::fail_count&)::operator()(boost::beast::test::fail_count&) const::'lambda0'(boost::system::error_code, unsigned long), boost::beast::basic_multi_buffer<std::__1::allocator<char> > >, boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type>, boost::beast::detail::buffers_pair<true> >::lambda, boost::system::error_code>, std::__1::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0xabe69e)  
    #5 0x55de8f in boost::asio::detail::scheduler_operation::destroy() (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x55de8f)  
    #6 0x18c8b27 in boost::asio::detail::scheduler::shutdown() (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x18c8b27)  
    #7 0x18a08c7 in boost::asio::detail::service_registry::shutdown_services() (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x18a08c7)  
    #8 0x18a0606 in boost::asio::io_context::~io_context() (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x18a0606)  
    #9 0xa8a6c7 in boost::beast::websocket::close_test::testSuspend()::'lambda4'(boost::beast::test::fail_count&)::operator()(boost::beast::test::fail_count&) const (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0xa8a6c7)  
    #10 0x97f8c2 in void boost::beast::websocket::websocket_test_suite::doFailLoop<boost::beast::websocket::close_test::testSuspend()::'lambda4'(boost::beast::test::fail_count&)>(boost::beast::websocket::close_test::testSuspend()::'lambda4'(boost::beast::test::fail_count&) const&, unsigned long) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x97f8c2)  
    #11 0x77873f in boost::beast::websocket::close_test::testSuspend() (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x77873f)  
    #12 0x772ba5 in boost::beast::websocket::close_test::run() (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x772ba5)  
    #13 0x532cc8 in void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x532cc8)  
    #14 0x51ceda in void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x51ceda)  
    #15 0x77276d in boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::websocket::close_test>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, bool)::'lambda'(boost::beast::unit_test::runner&)::operator()(boost::beast::unit_test::runner&) const (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x77276d)  
    #16 0x189f9e2 in bool boost::beast::unit_test::runner::run<void>(boost::beast::unit_test::suite_info const&) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x189f9e2)  
    #17 0x18941ac in bool boost::beast::unit_test::runner::run_each<boost::beast::unit_test::suite_list>(boost::beast::unit_test::suite_list const&) (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x18941ac)  
    #18 0x189296f in main (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x189296f)  
    #19 0x7f9a2281f82f in __libc_start_main /build/glibc-LK5gWL/glibc-2.23/csu/../csu/libc-start.c:291  
    #20 0x436d98 in _start (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x436d98)  
  
Address 0x7ffd687274b0 is located in stack of thread T0 at offset 21136 in frame  
    #0 0x18c848f in boost::asio::detail::scheduler::shutdown() (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x18c848f)  
  
  This frame has 1 object(s):  
    [32, 48) 'lock' <== Memory access at offset 21136 overflows this variable  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-use-after-scope (/__w/1/boost-root/bin.v2/libs/beast/test/beast/websocket/clang-linux-8.0.1/beast_ubasan/cxxstd-14-iso/threadapi-pthread/threading-multi/visibility-hidden/fat-tests+0x9f51a9) in boost::beast::basic_multi_buffer<std::__1::allocator<char> >::mutable_buffers_type::begin() const  
Shadow bytes around the buggy address:  
  0x10002d0dce40: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10002d0dce50: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10002d0dce60: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10002d0dce70: 00 00 00 00 00 00 00 00 00 00 f8 f8 00 00 f8 00  
  0x10002d0dce80: 00 00 f8 f8 f8 00 00 00 00 00 00 00 00 00 00 00  
=>0x10002d0dce90: 00 00 f8 f8 f8 f8[f8]f8 f8 f8 f8 00 00 00 00 00  
  0x10002d0dcea0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10002d0dceb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10002d0dcec0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10002d0dced0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10002d0dcee0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
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
==3405==ABORTING  
```  
https://dev.azure.com/damianjarek93/beast/_build/results?buildId=183&view=logs&jobId=12a660bf-1c90-53ce-248d-5e7828ae5148&taskId=1bea1fb5-dccf-5a90-cb26-e34f53c56ca7&lineStart=420&lineEnd=484&colStart=1&colEnd=17
