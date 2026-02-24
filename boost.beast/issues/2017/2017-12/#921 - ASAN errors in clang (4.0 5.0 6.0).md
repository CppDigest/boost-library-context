# #921 - ASAN errors in clang (4.0 5.0 6.0) [Closed]

> Username: djarek  
> Created at: 2017-12-03 11:26:37 UTC  
> Updated at: 2018-02-27 16:58:53 UTC  
> Closed at: 2018-02-27 16:58:53 UTC  
> Url: https://github.com/boostorg/beast/issues/921  

```  
====== BEGIN OUTPUT ======  
beast.websocket.close  
=================================================================  
==11582==ERROR: AddressSanitizer: stack-use-after-scope on address 0x7ffea4394640 at pc 0x0000009aaa11 bp 0x7ffea438d690 sp 0x7ffea438d688  
READ of size 8 at 0x7ffea4394640 thread T0  
    #0 0x9aaa10 in boost::intrusive::iiterator_members<boost::intrusive::list_node<void*>*, boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u> const*, false>::iiterator_members(boost::intrusive::list_node<void*>* const&, boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u> const* const&) /home/damian/projects/boost/./boost/intrusive/detail/iiterator.hpp:110:18  
    #1 0x9bb3b4 in boost::intrusive::list_iterator<boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u>, true>::list_iterator(boost::intrusive::list_iterator<boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u>, false> const&) /home/damian/projects/boost/./boost/intrusive/detail/list_iterator.hpp:66:10  
    #2 0x9c240c in boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type::begin() const /home/damian/projects/boost/./boost/beast/core/impl/multi_buffer.ipp:402:32  
    #3 0x9c21b9 in decltype (({parm#1}.begin)()) boost::asio::buffer_sequence_begin<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type>(boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type&) /home/damian/projects/boost/./boost/asio/buffer.hpp:438:12  
    #4 0x9c8831 in boost::beast::buffers_suffix<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type>::buffers_suffix(boost::beast::buffers_suffix<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type>&&) /home/damian/projects/boost/./boost/beast/core/impl/buffers_suffix.ipp:129:13  
    #5 0xb2f071 in boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#2}> >::read_some_op(boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#2}&&) /home/damian/projects/boost/./boost/beast/websocket/impl/read.ipp:61:5  
    #6 0xb34eee in boost::beast::detail::bound_handler<boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#2}> >, boost::system&, int>::bound_handler(boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#2}> >&&) /home/damian/projects/boost/./boost/beast/core/detail/bind_handler.hpp:111:5  
    #7 0xb34236 in boost::asio::detail::work_dispatcher<boost::beast::detail::bound_handler<boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#2}> >, boost::system&, int> >::work_dispatcher(boost::system&) /home/damian/projects/boost/./boost/asio/detail/work_dispatcher.hpp:49:7  
    #8 0xb33f9b in boost::asio::detail::executor_op<boost::asio::detail::work_dispatcher<boost::beast::detail::bound_handler<boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#2}> >, boost::system&, int> >, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::work_dispatcher<boost::beast::detail::bound_handler<boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#2}> >, boost::system&, int> >*, boost::system const&, unsigned long) /home/damian/projects/boost/./boost/asio/detail/executor_op.hpp:62:13  
    #9 0x54565e in boost::asio::detail::scheduler_operation::destroy() /home/damian/projects/boost/./boost/asio/detail/scheduler_operation.hpp:45:5  
    #10 0x544a00 in boost::asio::detail::scheduler::shutdown() /home/damian/projects/boost/./boost/asio/detail/impl/scheduler.ipp:120:10  
    #11 0x52c02c in boost::asio::detail::service_registry::shutdown_services() /home/damian/projects/boost/./boost/asio/detail/impl/service_registry.ipp:44:14  
    #12 0x52bedc in boost::asio::execution_context::shutdown() /home/damian/projects/boost/./boost/asio/impl/execution_context.ipp:41:22  
    #13 0x52b238 in boost::asio::execution_context::~execution_context() /home/damian/projects/boost/./boost/asio/impl/execution_context.ipp:34:3  
    #14 0x53fc24 in boost::asio::io_context::~io_context() /home/damian/projects/boost/./boost/asio/impl/io_context.ipp:57:1  
    #15 0xada9d7 in boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}::operator()(boost::beast::test::fail_counter&) const /home/damian/projects/boost/libs/beast/test/beast/websocket/close.cpp:403:9  
    #16 0x8f1dce in void boost::beast::websocket::websocket_test_suite::doFailLoop<boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6}>(boost::beast::websocket::close_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#6} const&, unsigned long) /home/damian/projects/boost/libs/beast/test/beast/websocket/test.hpp:275:17  
    #17 0x54e871 in boost::beast::websocket::close_test::testSuspend() /home/damian/projects/boost/libs/beast/test/beast/websocket/close.cpp:362:9  
    #18 0x53d3f1 in boost::beast::websocket::close_test::run() /home/damian/projects/boost/libs/beast/test/beast/websocket/close.cpp:629:9  
    #19 0xe350e3 in void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/suite.hpp:596:9  
    #20 0x53d244 in void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/suite.hpp:435:9  
    #21 0x53d03d in boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::websocket::close_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/suite_info.hpp:117:13  
    #22 0x53cd91 in std::_Function_handler<void (boost::beast::unit_test::runner&), boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::websocket::close_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}>::_M_invoke(std::_Any_data const&, boost::beast::unit_test::runner&) /usr/bin/../lib/gcc/x86_64-linux-gnu/7.0.1/../../../../include/c++/7.0.1/bits/std_function.h:316:2  
    #23 0xe47758 in std::function<void (boost::beast::unit_test::runner&)>::operator()(boost::beast::unit_test::runner&) const /usr/bin/../lib/gcc/x86_64-linux-gnu/7.0.1/../../../../include/c++/7.0.1/bits/std_function.h:706:14  
    #24 0xe476bb in boost::beast::unit_test::suite_info::run(boost::beast::unit_test::runner&) const /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/suite_info.hpp:86:9  
    #25 0xe466ff in bool boost::beast::unit_test::runner::run<void>(boost::beast::unit_test::suite_info const&) /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/runner.hpp:189:7  
    #26 0xe37b4b in bool boost::beast::unit_test::runner::run_each<boost::beast::unit_test::suite_list>(boost::beast::unit_test::suite_list const&) /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/runner.hpp:224:18  
    #27 0xe3625b in main /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/main.cpp:82:20  
    #28 0x7f07e82523f0 in __libc_start_main /build/glibc-mXZSwJ/glibc-2.24/csu/../csu/libc-start.c:291  
    #29 0x429da9 in _start (/home/damian/projects/boost/bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close+0x429da9)  
  
Address 0x7ffea4394640 is located in stack of thread T0 at offset 26208 in frame  
    #0 0x54482f in boost::asio::detail::scheduler::shutdown() /home/damian/projects/boost/./boost/asio/detail/impl/scheduler.ipp:109  
  
  This frame has 1 object(s):  
    [32, 48) 'lock' (line 110) <== Memory access at offset 26208 overflows this variable  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism or swapcontext  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-use-after-scope /home/damian/projects/boost/./boost/intrusive/detail/iiterator.hpp:110:18 in boost::intrusive::iiterator_members<boost::intrusive::list_node<void*>*, boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u> const*, false>::iiterator_members(boost::intrusive::list_node<void*>* const&, boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u> const* const&)  
Shadow bytes around the buggy address:  
  0x10005486a870: f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8  
  0x10005486a880: f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 00 00 00  
  0x10005486a890: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005486a8a0: 00 00 00 00 00 00 00 00 00 00 f8 00 00 00 f8 f8  
  0x10005486a8b0: f8 f8 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x10005486a8c0: 00 00 00 00 f8 f8 f8 f8[f8]f8 f8 f8 f8 00 00 00  
  0x10005486a8d0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005486a8e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005486a8f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005486a900: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005486a910: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
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
==11582==ABORTING  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/damian/projects/boost/bin.v2/libs/chrono/build/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi:/home/damian/projects/boost/bin.v2/libs/context/build/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi:/home/damian/projects/boost/bin.v2/libs/coroutine/build/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi:/home/damian/projects/boost/bin.v2/libs/filesystem/build/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi:/home/damian/projects/boost/bin.v2/libs/system/build/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi:/home/damian/projects/boost/bin.v2/libs/thread/build/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close"   > "bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close.output"  
    echo EXIT STATUS: $status >> "bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close.output"  
    if test $status -eq 0 ; then  
        cp "bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close.output" "bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output bin.v2/libs/beast/test/beast/websocket/close.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/close.run...  
clang-linux.compile.c++.without-pth bin.v2/libs/beast/test/beast/websocket/frame.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/frame.o  
clang-linux.link bin.v2/libs/beast/test/beast/websocket/frame.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/frame  
testing.capture-output bin.v2/libs/beast/test/beast/websocket/frame.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/frame.run  
**passed** bin.v2/libs/beast/test/beast/websocket/frame.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/frame.test  
clang-linux.compile.c++.without-pth bin.v2/libs/beast/test/beast/websocket/handshake.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/handshake.o  
clang-linux.link bin.v2/libs/beast/test/beast/websocket/handshake.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/handshake  
testing.capture-output bin.v2/libs/beast/test/beast/websocket/handshake.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/handshake.run  
**passed** bin.v2/libs/beast/test/beast/websocket/handshake.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/handshake.test  
clang-linux.compile.c++.without-pth bin.v2/libs/beast/test/beast/websocket/option.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/option.o  
clang-linux.link bin.v2/libs/beast/test/beast/websocket/option.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/option  
testing.capture-output bin.v2/libs/beast/test/beast/websocket/option.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/option.run  
**passed** bin.v2/libs/beast/test/beast/websocket/option.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/option.test  
clang-linux.compile.c++.without-pth bin.v2/libs/beast/test/beast/websocket/ping.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/ping.o  
clang-linux.link bin.v2/libs/beast/test/beast/websocket/ping.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/ping  
testing.capture-output bin.v2/libs/beast/test/beast/websocket/ping.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/ping.run  
**passed** bin.v2/libs/beast/test/beast/websocket/ping.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/ping.test  
clang-linux.compile.c++.without-pth bin.v2/libs/beast/test/beast/websocket/read.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/read.o  
clang-linux.link bin.v2/libs/beast/test/beast/websocket/read.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/read  
testing.capture-output bin.v2/libs/beast/test/beast/websocket/read.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/read.run  
====== BEGIN OUTPUT ======  
beast.websocket.read  
=================================================================  
==12134==ERROR: AddressSanitizer: stack-use-after-scope on address 0x7fff5b1e6b80 at pc 0x0000007be8e1 bp 0x7fff5b1dfbf0 sp 0x7fff5b1dfbe8  
READ of size 8 at 0x7fff5b1e6b80 thread T0  
    #0 0x7be8e0 in boost::intrusive::iiterator_members<boost::intrusive::list_node<void*>*, boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u> const*, false>::iiterator_members(boost::intrusive::list_node<void*>* const&, boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u> const* const&) /home/damian/projects/boost/./boost/intrusive/detail/iiterator.hpp:110:18  
    #1 0x7c79b4 in boost::intrusive::list_iterator<boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u>, true>::list_iterator(boost::intrusive::list_iterator<boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u>, false> const&) /home/damian/projects/boost/./boost/intrusive/detail/list_iterator.hpp:66:10  
    #2 0x7ce84c in boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type::begin() const /home/damian/projects/boost/./boost/beast/core/impl/multi_buffer.ipp:402:32  
    #3 0x7cfc29 in decltype (({parm#1}.begin)()) boost::asio::buffer_sequence_begin<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type>(boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type&) /home/damian/projects/boost/./boost/asio/buffer.hpp:438:12  
    #4 0x852961 in boost::beast::buffers_suffix<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type>::buffers_suffix(boost::beast::buffers_suffix<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type>&&) /home/damian/projects/boost/./boost/beast/core/impl/buffers_suffix.ipp:129:13  
    #5 0xbfd7e1 in boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#1}> >::read_some_op(boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#1}&&) /home/damian/projects/boost/./boost/beast/websocket/impl/read.ipp:61:5  
    #6 0xbfea86 in boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#1}> > >::work_dispatcher(boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#1}>&&) /home/damian/projects/boost/./boost/asio/detail/work_dispatcher.hpp:49:7  
    #7 0xbfe7de in boost::asio::detail::executor_op<boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#1}> > >, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::work_dispatcher<boost::beast::websocket::stream<boost::beast::test::stream>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type, boost::beast::websocket::stream<boost::beast::test::stream>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >, boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1}::operator()(boost::beast::test::fail_counter&) const::{lambda(boost::system::error_code, unsigned long)#1}> > >*, boost::system const&, unsigned long) /home/damian/projects/boost/./boost/asio/detail/executor_op.hpp:62:13  
    #8 0x54582e in boost::asio::detail::scheduler_operation::destroy() /home/damian/projects/boost/./boost/asio/detail/scheduler_operation.hpp:45:5  
    #9 0x544bd0 in boost::asio::detail::scheduler::shutdown() /home/damian/projects/boost/./boost/asio/detail/impl/scheduler.ipp:120:10  
    #10 0x52c1ec in boost::asio::detail::service_registry::shutdown_services() /home/damian/projects/boost/./boost/asio/detail/impl/service_registry.ipp:44:14  
    #11 0x52c09c in boost::asio::execution_context::shutdown() /home/damian/projects/boost/./boost/asio/impl/execution_context.ipp:41:22  
    #12 0x52b3f8 in boost::asio::execution_context::~execution_context() /home/damian/projects/boost/./boost/asio/impl/execution_context.ipp:34:3  
    #13 0x53fdf4 in boost::asio::io_context::~io_context() /home/damian/projects/boost/./boost/asio/impl/io_context.ipp:57:1  
    #14 0xbc11dc in boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1}::operator()(boost::beast::test::fail_counter&) const /home/damian/projects/boost/libs/beast/test/beast/websocket/read.cpp:684:9  
    #15 0xbbe43e in void boost::beast::websocket::websocket_test_suite::doFailLoop<boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1}>(boost::beast::websocket::read_test::testSuspend()::{lambda(boost::beast::test::fail_counter&)#1} const&, unsigned long) /home/damian/projects/boost/libs/beast/test/beast/websocket/test.hpp:275:17  
    #16 0x54f3e3 in boost::beast::websocket::read_test::testSuspend() /home/damian/projects/boost/libs/beast/test/beast/websocket/read.cpp:655:9  
    #17 0x53d5b1 in boost::beast::websocket::read_test::run() /home/damian/projects/boost/libs/beast/test/beast/websocket/read.cpp:1049:9  
    #18 0xe6d503 in void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/suite.hpp:596:9  
    #19 0x53d404 in void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/suite.hpp:435:9  
    #20 0x53d1fd in boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::websocket::read_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/suite_info.hpp:117:13  
    #21 0x53cf51 in std::_Function_handler<void (boost::beast::unit_test::runner&), boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::websocket::read_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}>::_M_invoke(std::_Any_data const&, boost::beast::unit_test::runner&) /usr/bin/../lib/gcc/x86_64-linux-gnu/7.0.1/../../../../include/c++/7.0.1/bits/std_function.h:316:2  
    #22 0xe7f9f8 in std::function<void (boost::beast::unit_test::runner&)>::operator()(boost::beast::unit_test::runner&) const /usr/bin/../lib/gcc/x86_64-linux-gnu/7.0.1/../../../../include/c++/7.0.1/bits/std_function.h:706:14  
    #23 0xe7f95b in boost::beast::unit_test::suite_info::run(boost::beast::unit_test::runner&) const /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/suite_info.hpp:86:9  
    #24 0xe7e99f in bool boost::beast::unit_test::runner::run<void>(boost::beast::unit_test::suite_info const&) /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/runner.hpp:189:7  
    #25 0xe6ff1b in bool boost::beast::unit_test::runner::run_each<boost::beast::unit_test::suite_list>(boost::beast::unit_test::suite_list const&) /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/runner.hpp:224:18  
    #26 0xe6e65b in main /home/damian/projects/boost/libs/beast/subtree/unit_test/include/boost/beast/unit_test/main.cpp:82:20  
    #27 0x7fd1d87923f0 in __libc_start_main /build/glibc-mXZSwJ/glibc-2.24/csu/../csu/libc-start.c:291  
    #28 0x429f69 in _start (/home/damian/projects/boost/bin.v2/libs/beast/test/beast/websocket/read.test/clang-gnu-linux-4.0/debug/threadapi-pthread/threading-multi/read+0x429f69)  
  
Address 0x7fff5b1e6b80 is located in stack of thread T0 at offset 26240 in frame  
    #0 0x5449ff in boost::asio::detail::scheduler::shutdown() /home/damian/projects/boost/./boost/asio/detail/impl/scheduler.ipp:109  
  
  This frame has 1 object(s):  
    [32, 48) 'lock' (line 110) <== Memory access at offset 26240 overflows this variable  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism or swapcontext  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-use-after-scope /home/damian/projects/boost/./boost/intrusive/detail/iiterator.hpp:110:18 in boost::intrusive::iiterator_members<boost::intrusive::list_node<void*>*, boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u> const*, false>::iiterator_members(boost::intrusive::list_node<void*>* const&, boost::intrusive::bhtraits<boost::beast::basic_multi_buffer<std::allocator<char> >::element, boost::intrusive::list_node_traits<void*>, (boost::intrusive::link_mode_type)0, boost::intrusive::dft_tag, 1u> const* const&)  
Shadow bytes around the buggy address:  
  0x10006b634d20: f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8 f8  
  0x10006b634d30: f8 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10006b634d40: 00 00 00 00 00 00 00 00 00 00 f8 00 00 00 00 00  
  0x10006b634d50: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10006b634d60: 00 00 00 00 00 00 00 00 00 00 00 00 f8 f8 f8 f8  
=>0x10006b634d70:[f8]f8 f8 f8 f8 00 00 00 00 00 00 00 00 00 00 00  
  0x10006b634d80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10006b634d90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10006b634da0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10006b634db0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10006b634dc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
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
==12134==ABORTING  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
Reproduces both in develop and in v148. Might be a false positive?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-04 19:22:10 UTC  
> Url: https://github.com/boostorg/beast/issues/921#issuecomment-349075789  

idk about this....

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-02-27 16:58:53 UTC  
> Url: https://github.com/boostorg/beast/issues/921#issuecomment-368948732  

There's nothing actionable here so I'll close the issue
