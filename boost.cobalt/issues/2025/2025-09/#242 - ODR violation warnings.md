# #242 - ODR violation warnings [Closed]

> Username: Lastique  
> Created at: 2025-09-03 18:37:33 UTC  
> Updated at: 2025-10-06 02:25:42 UTC  
> Closed at: 2025-10-06 02:25:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/242  

Building Boost.Cobalt from Boost 1.89.0 with gcc 13.3 (on Ubuntu 24.04) produces a number of ODR violation warnings:  
  
```  
./boost/cobalt/io/endpoint.hpp:51:8: warning: type ‘struct protocol_type’ violates the C++ One Definition Rule [-Wodr]  
   51 | struct protocol_type  
      |        ^  
./boost/cobalt/io/endpoint.hpp:51:8: note: a different type is defined in another translation unit  
   51 | struct protocol_type  
      |        ^  
./boost/cobalt/io/endpoint.hpp:90:14: note: the first difference of corresponding definitions is field ‘protocol_’  
   90 |   protocol_t protocol_ = static_cast<protocol_t>(0);  
      |              ^  
./boost/cobalt/io/endpoint.hpp:90:14: note: a field of same name but different type is defined in another translation unit  
   90 |   protocol_t protocol_ = static_cast<protocol_t>(0);  
      |              ^  
./boost/cobalt/io/endpoint.hpp:51:8: note: type ‘protocol_t’ defined in anonymous namespace cannot match across the translation unit boundary  
   51 | struct protocol_type  
      |        ^  
./boost/cobalt/io/endpoint.hpp:137:8: warning: type ‘struct endpoint’ violates the C++ One Definition Rule [-Wodr]  
  137 | struct endpoint  
      |        ^  
./boost/cobalt/io/endpoint.hpp:137:8: note: a different type is defined in another translation unit  
  137 | struct endpoint  
      |        ^  
./boost/cobalt/io/endpoint.hpp:219:29: note: the first difference of corresponding definitions is field ‘protocol_’  
  219 |   protocol_type::protocol_t protocol_ = static_cast<protocol_type::protocol_t>(0);  
      |                             ^  
./boost/cobalt/io/endpoint.hpp:219:29: note: a field of same name but different type is defined in another translation unit  
  219 |   protocol_type::protocol_t protocol_ = static_cast<protocol_type::protocol_t>(0);  
      |                             ^  
./boost/cobalt/io/endpoint.hpp:137:8: note: type ‘protocol_t’ defined in anonymous namespace cannot match across the translation unit boundary  
  137 | struct endpoint  
      |        ^  
./boost/asio/detail/reactive_socket_service.hpp:64:10: warning: type ‘struct implementation_type’ violates the C++ One Definition Rule [-Wodr]  
   64 |   struct implementation_type :  
      |          ^  
./boost/asio/detail/reactive_socket_service.hpp:64:10: note: a different type is defined in another translation unit  
   64 |   struct implementation_type :  
      |          ^  
./boost/asio/detail/reactive_socket_service.hpp:74:19: note: the first difference of corresponding definitions is field ‘protocol_’  
   74 |     protocol_type protocol_;  
      |                   ^  
./boost/asio/detail/reactive_socket_service.hpp:74:19: note: a field of same name but different type is defined in another translation unit  
   74 |     protocol_type protocol_;  
      |                   ^  
./boost/asio/detail/reactive_socket_service.hpp:64:10: note: type ‘struct protocol_type’ itself violates the C++ One Definition Rule  
   64 |   struct implementation_type :  
      |          ^  
./boost/asio/detail/io_object_impl.hpp:34:7: warning: type ‘struct io_object_impl’ violates the C++ One Definition Rule [-Wodr]  
   34 | class io_object_impl  
      |       ^  
./boost/asio/detail/io_object_impl.hpp:34:7: note: a different type is defined in another translation unit  
   34 | class io_object_impl  
      |       ^  
./boost/asio/detail/io_object_impl.hpp:167:23: note: the first difference of corresponding definitions is field ‘implementation_’  
  167 |   implementation_type implementation_;  
      |                       ^  
./boost/asio/detail/io_object_impl.hpp:167:23: note: a field of same name but different type is defined in another translation unit  
  167 |   implementation_type implementation_;  
      |                       ^  
./boost/asio/detail/io_object_impl.hpp:34:7: note: type ‘struct implementation_type’ itself violates the C++ One Definition Rule  
   34 | class io_object_impl  
      |       ^  
./boost/asio/basic_socket.hpp:66:7: warning: type ‘struct basic_socket’ violates the C++ One Definition Rule [-Wodr]  
   66 | class basic_socket  
      |       ^  
./boost/asio/basic_socket.hpp:66:7: note: a different type is defined in another translation unit  
   66 | class basic_socket  
      |       ^  
./boost/asio/basic_socket.hpp:1849:58: note: the first difference of corresponding definitions is field ‘impl_’  
 1849 |     detail::reactive_socket_service<Protocol>, Executor> impl_;  
      |                                                          ^  
./boost/asio/basic_socket.hpp:1849:58: note: a field of same name but different type is defined in another translation unit  
 1849 |     detail::reactive_socket_service<Protocol>, Executor> impl_;  
      |                                                          ^  
./boost/asio/detail/io_object_impl.hpp:34:7: note: type ‘struct io_object_impl’ itself violates the C++ One Definition Rule  
   34 | class io_object_impl  
      |       ^  
./boost/asio/basic_stream_socket.hpp:61:7: warning: type ‘struct basic_stream_socket’ violates the C++ One Definition Rule [-Wodr]  
   61 | class basic_stream_socket  
      |       ^  
./boost/asio/basic_stream_socket.hpp:61:7: note: a type with different bases is defined in another translation unit  
   61 | class basic_stream_socket  
      |       ^  
./boost/cobalt/io/stream_socket.hpp:21:29: warning: type ‘struct stream_socket’ violates the C++ One Definition Rule [-Wodr]  
   21 | struct BOOST_SYMBOL_VISIBLE stream_socket final : socket, stream  
      |                             ^  
./boost/cobalt/io/stream_socket.hpp:21:29: note: a different type is defined in another translation unit  
   21 | struct BOOST_SYMBOL_VISIBLE stream_socket final : socket, stream  
      |                             ^  
./boost/cobalt/io/stream_socket.hpp:46:54: note: the first difference of corresponding definitions is field ‘stream_socket_’  
   46 |   asio::basic_stream_socket<protocol_type, executor> stream_socket_;  
      |                                                      ^  
./boost/cobalt/io/stream_socket.hpp:46:54: note: a field of same name but different type is defined in another translation unit  
   46 |   asio::basic_stream_socket<protocol_type, executor> stream_socket_;  
      |                                                      ^  
./boost/asio/basic_stream_socket.hpp:61:7: note: type ‘struct basic_stream_socket’ itself violates the C++ One Definition Rule  
   61 | class basic_stream_socket  
      |       ^  
./boost/asio/basic_seq_packet_socket.hpp:56:7: warning: type ‘struct basic_seq_packet_socket’ violates the C++ One Definition Rule [-Wodr]  
   56 | class basic_seq_packet_socket  
      |       ^  
./boost/asio/basic_seq_packet_socket.hpp:56:7: note: a type with different bases is defined in another translation unit  
   56 | class basic_seq_packet_socket  
      |       ^  
./boost/cobalt/io/seq_packet_socket.hpp:20:29: warning: type ‘struct seq_packet_socket’ violates the C++ One Definition Rule [-Wodr]  
   20 | struct BOOST_SYMBOL_VISIBLE seq_packet_socket final : socket  
      |                             ^  
./boost/cobalt/io/seq_packet_socket.hpp:20:29: note: a different type is defined in another translation unit  
   20 | struct BOOST_SYMBOL_VISIBLE seq_packet_socket final : socket  
      |                             ^  
./boost/cobalt/io/seq_packet_socket.hpp:77:58: note: the first difference of corresponding definitions is field ‘seq_packet_socket_’  
   77 |   asio::basic_seq_packet_socket<protocol_type, executor> seq_packet_socket_;  
      |                                                          ^  
./boost/cobalt/io/seq_packet_socket.hpp:77:58: note: a field of same name but different type is defined in another translation unit  
   77 |   asio::basic_seq_packet_socket<protocol_type, executor> seq_packet_socket_;  
      |                                                          ^  
./boost/asio/basic_seq_packet_socket.hpp:56:7: note: type ‘struct basic_seq_packet_socket’ itself violates the C++ One Definition Rule  
   56 | class basic_seq_packet_socket  
      |       ^  
./boost/asio/detail/reactive_socket_service.hpp:138:29: warning: ‘assign’ violates the C++ One Definition Rule [-Wodr]  
  138 |   boost::system::error_code assign(implementation_type& impl,  
      |                             ^  
./boost/asio/detail/reactive_socket_service.hpp:138:29: note: type mismatch in parameter 1  
  138 |   boost::system::error_code assign(implementation_type& impl,  
      |                             ^  
./boost/asio/detail/reactive_socket_service.hpp:64:10: note: type ‘struct implementation_type’ itself violates the C++ One Definition Rule  
   64 |   struct implementation_type :  
      |          ^  
./boost/asio/detail/reactive_socket_service.hpp:138:29: note: ‘assign’ was previously declared here  
  138 |   boost::system::error_code assign(implementation_type& impl,  
      |                             ^  
./boost/asio/detail/reactive_socket_service.hpp:126:29: warning: ‘open’ violates the C++ One Definition Rule [-Wodr]  
  126 |   boost::system::error_code open(implementation_type& impl,  
      |                             ^  
./boost/asio/detail/reactive_socket_service.hpp:126:29: note: type mismatch in parameter 1  
  126 |   boost::system::error_code open(implementation_type& impl,  
      |                             ^  
./boost/asio/detail/reactive_socket_service.hpp:64:10: note: type ‘struct implementation_type’ itself violates the C++ One Definition Rule  
   64 |   struct implementation_type :  
      |          ^  
./boost/asio/detail/reactive_socket_service.hpp:126:29: note: ‘open’ was previously declared here  
  126 |   boost::system::error_code open(implementation_type& impl,  
      |                             ^  
./boost/asio/detail/reactive_socket_service.hpp:195:17: warning: ‘local_endpoint’ violates the C++ One Definition Rule [-Wodr]  
  195 |   endpoint_type local_endpoint(const implementation_type& impl,  
      |                 ^  
./boost/asio/detail/reactive_socket_service.hpp:195:17: note: return value type mismatch  
  195 |   endpoint_type local_endpoint(const implementation_type& impl,  
      |                 ^  
./boost/asio/detail/reactive_socket_service.hpp:195:17: note: type ‘struct endpoint_type’ itself violates the C++ One Definition Rule  
./boost/asio/detail/reactive_socket_service.hpp:195:17: note: ‘local_endpoint’ was previously declared here  
./boost/asio/detail/reactive_socket_service.hpp:167:29: warning: ‘set_option’ violates the C++ One Definition Rule [-Wodr]  
  167 |   boost::system::error_code set_option(implementation_type& impl,  
      |                             ^  
./boost/asio/detail/reactive_socket_service.hpp:167:29: note: type mismatch in parameter 1  
  167 |   boost::system::error_code set_option(implementation_type& impl,  
      |                             ^  
./boost/asio/detail/reactive_socket_service.hpp:64:10: note: type ‘struct implementation_type’ itself violates the C++ One Definition Rule  
   64 |   struct implementation_type :  
      |          ^  
./boost/asio/detail/reactive_socket_service.hpp:167:29: note: ‘set_option’ was previously declared here  
  167 |   boost::system::error_code set_option(implementation_type& impl,  
      |                             ^  
```  
  
The b2 command line is:  
  
```  
b2 -j 16 --toolset=gcc cxxflags="-flto=auto -ffat-lto-objects -march=nehalem -mtune=icelake-server -faligned-new -fcoroutines -fno-plt -fvisibility-inlines-hidden -O3 -fno-math-errno -fno-rounding-math -fno-signaling-nans -fno-trapping-math -ftree-vectorize -fgcse-sm -fgcse-las -fgcse-after-reload -ftree-loop-im -ftree-loop-distribution -ftree-loop-if-convert -funswitch-loops -frename-registers -fivopts" linkflags="-Wl,-z,relro -Wl,-z,now -Wl,-O1 -Wl,--hash-style=both -Wl,-z,noexecstack -Wl,--as-needed" \  
 variant=release cxxstd=20-gnu threading=multi debug-symbols=on runtime-link=shared optimization=speed inlining=full warnings=on link=shared --without-mpi --without-wave --without-python --without-graph stage  
```  
  
I think, this is caused by how `protocol_t`, `type_t` and `family_t` are defined in `boost/cobalt/io/endpoint.hpp`. On Linux, `IPPROTO_TCP` and other similar constants are defined in `/usr/include/netinet/in.h` as values of anonymous enums. This means, these constants will have distinct types in every translation unit, although the values will be the same. But Boost.Cobalt uses the type of these constants to define its `protocol_t`, `type_t` and `family_t`, which makes these types differ between TUs.  
  
I suggest using fixed width integer types for Boost.Cobalt types. If you still want to, you can pick the integer types that are of the same size as the constants, but you will most likely arrive to `int` anyway.
