# #192 - Static Code Analysis Findings [Open]

> Username: codeanalysis9876  
> Created at: 2018-09-09 15:20:59 UTC  
> Updated at: 2024-12-05 23:15:04 UTC  
> Url: https://github.com/boostorg/boost/issues/192  

Buffer Overflow:   Format String - At Line: 1980 of File:   opt/buildtools-local/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
--  
Out-of-Bounds Read: Off-by-One - At Line:   728 of File:   opt/buildtools-local/gcc-4.8.2/boost-1.55.0/include/boost/iterator/iterator_facade.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 47 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/algorithm/string/detail/util.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 52 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/algorithm/string/detail/util.hpp  
Dead Code - At Line: 331 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/basic_streambuf.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 54 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/completion_condition.hpp  
Dead Code - At Line: 148 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/datagram_socket_service.hpp  
Memory Leak - At Line: 183 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/deadline_timer_service.hpp  
Dead Code - At Line: 228 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/epoll_reactor.ipp  
Dead Code - At Line: 231 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/epoll_reactor.ipp  
Dead Code - At Line: 240 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/epoll_reactor.ipp  
Dead Code - At Line: 263 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/epoll_reactor.ipp  
Poor Style: Variable Never Used - At   Line: 242 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/epoll_reactor.ipp  
Poor Style: Redundant Initialization - At   Line: 122 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/eventfd_select_interrupter.ipp  
Dead Code - At Line: 194 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/reactive_socket_service_base.ipp  
Dead Code - At Line: 195 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/reactive_socket_service_base.ipp  
Dead Code - At Line: 248 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/signal_set_service.ipp  
Poor Style: Redundant Initialization - At   Line: 71 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/signal_set_service.ipp  
Buffer Overflow: Format String - At Line:   1980 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Buffer Overflow: Format String - At Line:   1980 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Type Mismatch: Signed to Unsigned - At   Line: 1165 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Type Mismatch: Signed to Unsigned - At   Line: 779 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Type Mismatch: Signed to Unsigned - At   Line: 925 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Unreleased Resource - At Line: 1379 of   File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 1406 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 1411 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 1413 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 1415 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 1419 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 1494 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 1501 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 1507 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 397 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Dead Code - At Line: 446 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Missing Check against Null - At Line:   1562 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Unchecked Return Value - At Line: 342 of   File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Memory Leak - At Line: 63 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/task_io_service.hpp  
Unchecked Return Value - At Line: 52 of   File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/posix_event.hpp  
Unchecked Return Value - At Line: 62 of   File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/posix_event.hpp  
Unchecked Return Value - At Line: 80 of   File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/posix_event.hpp  
Memory Leak - At Line: 407 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/reactive_socket_service.hpp  
Memory Leak - At Line: 438 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/reactive_socket_service.hpp  
Memory Leak - At Line: 209 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/reactive_socket_service_base.hpp  
Memory Leak - At Line: 280 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/reactive_socket_service_base.hpp  
Memory Leak - At Line: 152 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/signal_set_service.hpp  
Null Dereference - At Line: 43 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/task_io_service_operation.hpp  
Dead Code - At Line: 84 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/timer_queue.hpp  
Dead Code - At Line: 275 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/impl/read.hpp  
Dead Code - At Line: 277 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/impl/read.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 164 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/impl/read_until.hpp  
Dead Code - At Line: 420 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/impl/read_until.hpp  
Dead Code - At Line: 338 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/impl/write.hpp  
Dead Code - At Line: 340 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/impl/write.hpp  
Dead Code - At Line: 109 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/ip/impl/address.ipp  
Dead Code - At Line: 119 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/ip/impl/address.ipp  
Dead Code - At Line: 55 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/ip/impl/address_v4.ipp  
Dead Code - At Line: 148 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/stream_socket_service.hpp  
Memory Leak - At Line: 129 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/detail/allocator_version_traits.hpp  
Dead Code - At Line: 142 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/detail/allocator_version_traits.hpp  
Dead Code - At Line: 598 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/detail/tree.hpp  
Poor Style: Variable Never Used - At   Line: 598 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/detail/tree.hpp  
Poor Style: Variable Never Used - At   Line: 607 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/detail/tree.hpp  
Memory Leak - At Line: 1258 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/list.hpp  
Memory Leak - At Line: 1458 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/slist.hpp  
Memory Leak - At Line: 806 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/slist.hpp  
Dead Code - At Line: 405 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/slist.hpp  
Memory Leak - At Line: 1679 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/vector.hpp  
Memory Leak - At Line: 1837 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/vector.hpp  
Memory Leak - At Line: 1896 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/vector.hpp  
Dead Code - At Line: 1663 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/container/vector.hpp  
Dead Code - At Line: 44 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/date_formatting.hpp  
Dead Code - At Line: 49 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/date_formatting.hpp  
Dead Code - At Line: 92 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/date_formatting.hpp  
Dead Code - At Line: 97 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/date_formatting.hpp  
Dead Code - At Line: 182 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/date_parsing.hpp  
Dead Code - At Line: 183 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/date_parsing.hpp  
Dead Code - At Line: 186 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/date_parsing.hpp  
Dead Code - At Line: 177 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/int_adapter.hpp  
Dead Code - At Line: 453 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/int_adapter.hpp  
Memory Leak - At Line: 60 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/posix_time/posix_time_io.hpp  
Dead Code - At Line: 237 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/time_parsing.hpp  
Dead Code - At Line: 242 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/time_parsing.hpp  
Dead Code - At Line: 247 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/time_parsing.hpp  
Dead Code - At Line: 287 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/time_parsing.hpp  
Poor Style: Variable Never Used - At   Line: 247 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/time_parsing.hpp  
Poor Style: Variable Never Used - At   Line: 248 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/time_parsing.hpp  
Dead Code - At Line: 154 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/time_system_counted.hpp  
Poor Style: Variable Never Used - At   Line: 154 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/time_system_counted.hpp  
Dead Code - At Line: 312 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/exception/detail/exception_ptr.hpp  
Dead Code - At Line: 318 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/exception/detail/exception_ptr.hpp  
Dead Code - At Line: 324 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/exception/detail/exception_ptr.hpp  
Dead Code - At Line: 328 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/exception/detail/exception_ptr.hpp  
Dead Code - At Line: 340 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/exception/detail/exception_ptr.hpp  
Dead Code - At Line: 794 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/filesystem/operations.hpp  
Dead Code - At Line: 920 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/filesystem/operations.hpp  
Out-of-Bounds Read: Off-by-One - At Line:   318 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/function/function_base.hpp  
Dead Code - At Line: 39 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/fusion/algorithm/query/detail/any.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 119 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/fusion/container/list/detail/at_impl.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 198 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/fusion/container/vector/detail/preprocessed/vector10.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 99 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/fusion/container/vector/detail/preprocessed/vector10.hpp  
Out-of-Bounds Read: Off-by-One - At Line:   67 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/intrusive/bstree.hpp  
Memory Leak - At Line: 256 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/intrusive/detail/utilities.hpp  
Dead Code - At Line: 171 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp  
Dead Code - At Line: 174 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp  
Poor Style: Variable Never Used - At   Line: 172 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp  
Poor Style: Redundant Initialization - At   Line: 172 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/iostreams/tee.hpp  
Poor Style: Redundant Initialization - At   Line: 173 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/iostreams/tee.hpp  
Out-of-Bounds Read: Off-by-One - At Line:   728 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/iterator/iterator_facade.hpp  
Dead Code - At Line: 2375 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/lexical_cast.hpp  
Dead Code - At Line: 71 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/multi_index/detail/auto_space.hpp  
Memory Leak - At Line: 598 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/multi_index_container.hpp  
Dead Code - At Line: 694 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/multiprecision/detail/number_base.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 488 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/optional/optional.hpp  
Dead Code - At Line: 240 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/optional/optional.hpp  
Dead Code - At Line: 449 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/pool/pool_alloc.hpp  
Memory Leak - At Line: 37 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/preprocessor/iteration/detail/local.hpp  
Poor Style: Value Never Read - At Line:   88 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/proto/expr.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 1153 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/proto/traits.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 1163 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/proto/traits.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 137 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/proto/transform/default.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 141 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/proto/transform/default.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 261 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/regex/v4/basic_regex.hpp  
Dead Code - At Line: 672 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/regex/v4/perl_matcher_common.hpp  
Poor Style: Variable Never Used - At   Line: 717 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/regex/v4/perl_matcher_common.hpp  
Poor Style: Value Never Read - At Line:   89 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/smart_ptr/detail/sp_counted_base_gcc_x86.hpp  
Use After Free - At Line: 277 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/smart_ptr/shared_ptr.hpp  
Use After Free - At Line: 277 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/smart_ptr/shared_ptr.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 23 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/detail/attributes.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 63 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/detail/indirect_iterator.hpp  
Poor Style: Variable Never Used - At   Line: 79 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/directive/omit.hpp  
Dead Code - At Line: 154 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/generate.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 498 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/numeric/detail/numeric_utils.hpp  
Poor Style: Value Never Read - At Line:   739 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/numeric/detail/numeric_utils.hpp  
Poor Style: Redundant Initialization - At   Line: 97 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/numeric/detail/real_utils.hpp  
Poor Style: Variable Never Used - At   Line: 104 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/numeric/detail/real_utils.hpp  
Poor Style: Value Never Read - At Line:   295 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/numeric/real_policies.hpp  
Dead Code - At Line: 269 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/qi/detail/pass_container.hpp  
Poor Style: Redundant Initialization - At   Line: 262 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/qi/detail/pass_container.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 108 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/qi/numeric/detail/numeric_utils.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 109 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/qi/numeric/detail/numeric_utils.hpp  
Dead Code - At Line: 429 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/qi/numeric/detail/numeric_utils.hpp  
Dead Code - At Line: 165 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp  
Memory Leak - At Line: 127 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/qi/string/detail/tst.hpp  
Dead Code - At Line: 189 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/support/algorithm/any_if.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 692 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/support/char_encoding/iso8859_1.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 131 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/support/char_encoding/standard.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 176 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/support/char_encoding/standard_wide.hpp  
Memory Leak - At Line: 62 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/support/iterators/multi_pass.hpp  
Dead Code - At Line: 328 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/system/error_code.hpp  
Poor Style: Variable Never Used - At   Line: 441 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/unordered/detail/table.hpp  
Poor Style: Variable Never Used - At   Line: 547 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/unordered/detail/table.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 260 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/unordered/detail/unique.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 146 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/unordered/detail/util.hpp  
Insecure Randomness - At Line: 144 of   File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/uuid/seed_rng.hpp  
Insecure Randomness - At Line: 145 of   File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/uuid/seed_rng.hpp  
Insecure Randomness - At Line: 146 of   File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/uuid/seed_rng.hpp  
Null Dereference - At Line: 48 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/variant/detail/forced_return.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 48 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/variant/detail/forced_return.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 148 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/variant/get.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 163 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/variant/get.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 182 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/variant/get.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 198 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/variant/get.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 91 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/variant/get.hpp  
Type Mismatch: Signed to Unsigned - At   Line: 939 of File:   opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/variant/variant.hpp

---

## Comment 1

> Username: jeking3  
> Created at: 2018-09-27 22:28:13 UTC  
> Url: https://github.com/boostorg/boost/issues/192#issuecomment-425262658  

This is against boost 1.55.0 which is pretty old.

---

## Comment 2

> Username: codeanalysis9876  
> Created at: 2019-09-21 13:10:27 UTC  
> Url: https://github.com/boostorg/boost/issues/192#issuecomment-533796891  

Type Mismatch: Signed to Unsigned - At Line: 86 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/date_time/microsec_time_clock.hpp  
Type Mismatch: Signed to Unsigned - At Line: 62 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/thread_info_base.hpp  
Type Mismatch: Signed to Unsigned - At Line: 63 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/karma/detail/indirect_iterator.hpp  
Memory Leak - At Line: 407 of File: opt/buildtools-local/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/reactive_socket_service.hpp  
Type Mismatch: Signed to Unsigned - At Line: 747 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Type Mismatch: Signed to Unsigned - At Line: 859 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Type Mismatch: Signed to Unsigned - At Line: 1130 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Type Mismatch: Signed to Unsigned - At Line: 1223 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/impl/socket_ops.ipp  
Type Mismatch: Signed to Unsigned - At Line: 708 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/perl_matcher_non_recursive.hpp  
Type Mismatch: Signed to Unsigned - At Line: 782 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/perl_matcher_non_recursive.hpp  
Type Mismatch: Signed to Unsigned - At Line: 857 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/perl_matcher_non_recursive.hpp  
Memory Leak - At Line: 407 of File: opt/buildtools-local/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/reactive_socket_service.hpp  
Memory Leak - At Line: 438 of File: opt/buildtools-local/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/reactive_socket_service.hpp  
Type Mismatch: Signed to Unsigned - At Line: 62 of File: opt/buildtools-local/gcc-4.8.2/boost-1.55.0/include/boost/asio/detail/thread_info_base.hpp  
Type Mismatch: Signed to Unsigned - At Line: 453 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/chrono/duration.hpp  
Type Mismatch: Signed to Unsigned - At Line: 466 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/numeric/conversion/detail/converter.hpp  
Type Mismatch: Signed to Unsigned - At Line: 392 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/optional/optional.hpp  
Type Mismatch: Signed to Unsigned - At Line: 750 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/phoenix/stl/container/container.hpp  
Type Mismatch: Signed to Unsigned - At Line: 265 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/spirit/home/qi/numeric/uint.hpp  
Type Mismatch: Signed to Unsigned - At Line: 583 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/unordered/detail/allocate.hpp  
Dead Code - At Line: 91 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/ssl/impl/context.ipp  
Dead Code - At Line: 94 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/ssl/impl/context.ipp  
Dead Code - At Line: 97 of File: opt/buildtools-network/gcc-4.8.2/boost-1.55.0/include/boost/asio/ssl/impl/context.ipp

---

## Comment 3

> Username: jeking3  
> Created at: 2024-12-05 23:15:03 UTC  
> Url: https://github.com/boostorg/boost/issues/192#issuecomment-2521697906  

I would recommend you cut an issue to each repository affected, or submit pull requests to each repository.
