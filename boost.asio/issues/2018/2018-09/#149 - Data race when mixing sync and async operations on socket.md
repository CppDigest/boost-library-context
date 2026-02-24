# #149 - Data race when mixing sync and async operations on socket. [Closed]

> Username: Alkenso  
> Created at: 2018-09-23 18:23:41 UTC  
> Updated at: 2020-12-30 00:53:28 UTC  
> Closed at: 2020-12-30 00:53:27 UTC  
> Url: https://github.com/boostorg/asio/issues/149  

Data race while sync connect to socket when io_service is running on another thread.  
  
I've found strange data race (reported by Xcode Thread Sanitizer) while performing synchronous connect to the socket when io_service is already running in another thread.  
  
It seems like io_service::run tries to read memory of the object which has not been completely constructed.  
  
The issue is reproduces in boost 1.67 and 1.68, but with earlier versions (like versions of 2016 year) all ok.  
  
**Report example 1:**    
```  
.../boost_1_68_0/boost/asio/detail/conditionally_enabled_mutex.hpp: runtime: Threading Issues: Data race in boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&) at 0x7b2800000a00  
  
==================  
WARNING: ThreadSanitizer: data race (pid=27654)  
  Read of size 1 at 0x7b2800000a58 by thread T4:  
    #0 boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&) conditionally_enabled_mutex.hpp:53 (BoostAsioIssue:x86_64+0x10000e544)  
    #1 boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&) conditionally_enabled_mutex.hpp:52 (BoostAsioIssue:x86_64+0x10000e318)  
    #2 boost::asio::detail::kqueue_reactor::run(long, boost::asio::detail::op_queue<boost::asio::detail::scheduler_operation>&) kqueue_reactor.ipp:445 (BoostAsioIssue:x86_64+0x1000166c6)  
    #3 boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) scheduler.ipp:385 (BoostAsioIssue:x86_64+0x1000159bc)  
    #4 boost::asio::detail::scheduler::run(boost::system::error_code&) scheduler.ipp:154 (BoostAsioIssue:x86_64+0x1000154aa)  
    #5 boost::asio::io_context::run() io_context.ipp:62 (BoostAsioIssue:x86_64+0x100015068)  
    #6 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()::operator()() const main.cpp:21 (BoostAsioIssue:x86_64+0x100014f37)  
    #7 _ZNSt3__112__async_funcIZN12UdpTransportC1ERKNS_12basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEEEtNS_8functionIFvNS_6vectorIhNS5_IhEEEEEEEEUlvE_JEE9__executeIJEEEvNS_15__tuple_indicesIJXspT_EEEE type_traits:4323 (BoostAsioIssue:x86_64+0x100014ea0)  
    #8 std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()>::operator()() future:2323 (BoostAsioIssue:x86_64+0x100014e28)  
    #9 std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::__execute() future:1040 (BoostAsioIssue:x86_64+0x100014cd9)  
    #10 std::__1::__thread_proxy<std::__1::tuple<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct> >, void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*> >(void*, void*) type_traits:4264 (BoostAsioIssue:x86_64+0x100019698)  
  
  Previous write of size 1 at 0x7b2800000a58 by main thread:  
    [failed to restore the stack]  
  
  Location is heap block of size 160 at 0x7b2800000a00 allocated by main thread:  
    #0 operator new(unsigned long) <null>:2667232 (libclang_rt.tsan_osx_dynamic.dylib:x86_64h+0x6a983)  
    #1 boost::asio::detail::kqueue_reactor::descriptor_state* boost::asio::detail::object_pool_access::create<boost::asio::detail::kqueue_reactor::descriptor_state, bool>(bool) object_pool.hpp:41 (BoostAsioIssue:x86_64+0x100024d3a)  
    #2 boost::asio::detail::kqueue_reactor::descriptor_state* boost::asio::detail::object_pool<boost::asio::detail::kqueue_reactor::descriptor_state>::alloc<bool>(bool) object_pool.hpp:114 (BoostAsioIssue:x86_64+0x100024bf1)  
    #3 boost::asio::detail::kqueue_reactor::allocate_descriptor_state() kqueue_reactor.ipp:524 (BoostAsioIssue:x86_64+0x100024ab7)  
    #4 boost::asio::detail::kqueue_reactor::register_descriptor(int, boost::asio::detail::kqueue_reactor::descriptor_state*&) kqueue_reactor.ipp:141 (BoostAsioIssue:x86_64+0x1000246b0)  
    #5 boost::asio::detail::reactive_socket_service_base::do_open(boost::asio::detail::reactive_socket_service_base::base_implementation_type&, int, int, int, boost::system::error_code&) reactive_socket_service_base.ipp:186 (BoostAsioIssue:x86_64+0x100024130)  
    #6 boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>::open(boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>::implementation_type&, boost::asio::ip::udp const&, boost::system::error_code&) reactive_socket_service.hpp:127 (BoostAsioIssue:x86_64+0x100023c78)  
    #7 boost::asio::basic_socket<boost::asio::ip::udp>::connect(boost::asio::ip::basic_endpoint<boost::asio::ip::udp> const&, boost::system::error_code&) basic_socket.hpp:801 (BoostAsioIssue:x86_64+0x10001a451)  
    #8 UdpTransport::connect(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short) main.cpp:52 (BoostAsioIssue:x86_64+0x10000725e)  
    #9 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:25 (BoostAsioIssue:x86_64+0x100006a3c)  
    #10 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:24 (BoostAsioIssue:x86_64+0x1000034c3)  
    #11 main main.cpp:98 (BoostAsioIssue:x86_64+0x1000024fc)  
  
  Thread T4 (tid=448463, running) created by main thread at:  
    #0 pthread_create <null>:2667280 (libclang_rt.tsan_osx_dynamic.dylib:x86_64h+0x283ed)  
    #1 std::__1::thread::thread<void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*, void>(void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*&&)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*&&) __threading_support:327 (BoostAsioIssue:x86_64+0x100018bf0)  
    #2 std::__1::thread::thread<void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*, void>(void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*&&)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*&&) thread:360 (BoostAsioIssue:x86_64+0x100014ae8)  
    #3 std::__1::__make_async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >(std::__1::future<void>, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()>&&) future:2301 (BoostAsioIssue:x86_64+0x100013f86)  
    #4 std::__1::async<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()>(std::__1::future<std::__1::__invoke_of<std::__1::decay<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()>::type, std::__1::decay<>::type>::type>, std::__1::launch, UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()&&) future:2349 (BoostAsioIssue:x86_64+0x100006d83)  
    #5 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:18 (BoostAsioIssue:x86_64+0x100006a04)  
    #6 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:24 (BoostAsioIssue:x86_64+0x1000034c3)  
    #7 main main.cpp:98 (BoostAsioIssue:x86_64+0x1000024fc)  
  
SUMMARY: ThreadSanitizer: data race conditionally_enabled_mutex.hpp:53 in boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&)  
```  
  
**Report example 2:**  
```  
==================  
WARNING: ThreadSanitizer: data race (pid=27734)  
  Read of size 1 at 0x7b2800000a58 by thread T4:  
    #0 boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&) conditionally_enabled_mutex.hpp:53 (BoostAsioIssue:x86_64+0x10000e2a4)  
    #1 boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&) conditionally_enabled_mutex.hpp:52 (BoostAsioIssue:x86_64+0x10000e078)  
    #2 boost::asio::detail::kqueue_reactor::run(long, boost::asio::detail::op_queue<boost::asio::detail::scheduler_operation>&) kqueue_reactor.ipp:445 (BoostAsioIssue:x86_64+0x100016426)  
    #3 boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) scheduler.ipp:385 (BoostAsioIssue:x86_64+0x10001571c)  
    #4 boost::asio::detail::scheduler::run(boost::system::error_code&) scheduler.ipp:154 (BoostAsioIssue:x86_64+0x10001520a)  
    #5 boost::asio::io_context::run() io_context.ipp:62 (BoostAsioIssue:x86_64+0x100014dc8)  
    #6 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()::operator()() const main.cpp:21 (BoostAsioIssue:x86_64+0x100014c97)  
    #7 _ZNSt3__112__async_funcIZN12UdpTransportC1ERKNS_12basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEEEtNS_8functionIFvNS_6vectorIhNS5_IhEEEEEEEEUlvE_JEE9__executeIJEEEvNS_15__tuple_indicesIJXspT_EEEE type_traits:4323 (BoostAsioIssue:x86_64+0x100014c00)  
    #8 std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()>::operator()() future:2323 (BoostAsioIssue:x86_64+0x100014b88)  
    #9 std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::__execute() future:1040 (BoostAsioIssue:x86_64+0x100014a39)  
    #10 std::__1::__thread_proxy<std::__1::tuple<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct> >, void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*> >(void*, void*) type_traits:4264 (BoostAsioIssue:x86_64+0x1000193f8)  
  
  Previous write of size 1 at 0x7b2800000a58 by main thread (mutexes: write M293):  
    #0 boost::asio::detail::conditionally_enabled_mutex::conditionally_enabled_mutex(bool) conditionally_enabled_mutex.hpp:109 (BoostAsioIssue:x86_64+0x10000d558)  
    #1 boost::asio::detail::conditionally_enabled_mutex::conditionally_enabled_mutex(bool) conditionally_enabled_mutex.hpp:110 (BoostAsioIssue:x86_64+0x10000cebd)  
    #2 boost::asio::detail::kqueue_reactor::descriptor_state::descriptor_state(bool) kqueue_reactor.hpp:67 (BoostAsioIssue:x86_64+0x100024be2)  
    #3 boost::asio::detail::kqueue_reactor::descriptor_state::descriptor_state(bool) kqueue_reactor.hpp:67 (BoostAsioIssue:x86_64+0x100024b5d)  
    #4 boost::asio::detail::kqueue_reactor::descriptor_state* boost::asio::detail::object_pool_access::create<boost::asio::detail::kqueue_reactor::descriptor_state, bool>(bool) object_pool.hpp:41 (BoostAsioIssue:x86_64+0x100024abd)  
    #5 boost::asio::detail::kqueue_reactor::descriptor_state* boost::asio::detail::object_pool<boost::asio::detail::kqueue_reactor::descriptor_state>::alloc<bool>(bool) object_pool.hpp:114 (BoostAsioIssue:x86_64+0x100024951)  
    #6 boost::asio::detail::kqueue_reactor::allocate_descriptor_state() kqueue_reactor.ipp:524 (BoostAsioIssue:x86_64+0x100024817)  
    #7 boost::asio::detail::kqueue_reactor::register_descriptor(int, boost::asio::detail::kqueue_reactor::descriptor_state*&) kqueue_reactor.ipp:141 (BoostAsioIssue:x86_64+0x100024410)  
    #8 boost::asio::detail::reactive_socket_service_base::do_open(boost::asio::detail::reactive_socket_service_base::base_implementation_type&, int, int, int, boost::system::error_code&) reactive_socket_service_base.ipp:186 (BoostAsioIssue:x86_64+0x100023e90)  
    #9 boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>::open(boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>::implementation_type&, boost::asio::ip::udp const&, boost::system::error_code&) reactive_socket_service.hpp:127 (BoostAsioIssue:x86_64+0x1000239d8)  
    #10 boost::asio::basic_socket<boost::asio::ip::udp>::connect(boost::asio::ip::basic_endpoint<boost::asio::ip::udp> const&, boost::system::error_code&) basic_socket.hpp:801 (BoostAsioIssue:x86_64+0x10001a1b1)  
    #11 UdpTransport::connect(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short) main.cpp:52 (BoostAsioIssue:x86_64+0x100006fbe)  
    #12 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:25 (BoostAsioIssue:x86_64+0x10000679c)  
    #13 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:24 (BoostAsioIssue:x86_64+0x1000032b3)  
    #14 main main.cpp:95 (BoostAsioIssue:x86_64+0x10000265f)  
  
  Location is heap block of size 160 at 0x7b2800000a00 allocated by main thread:  
    #0 operator new(unsigned long) <null>:2667232 (libclang_rt.tsan_osx_dynamic.dylib:x86_64h+0x6a983)  
    #1 boost::asio::detail::kqueue_reactor::descriptor_state* boost::asio::detail::object_pool_access::create<boost::asio::detail::kqueue_reactor::descriptor_state, bool>(bool) object_pool.hpp:41 (BoostAsioIssue:x86_64+0x100024a9a)  
    #2 boost::asio::detail::kqueue_reactor::descriptor_state* boost::asio::detail::object_pool<boost::asio::detail::kqueue_reactor::descriptor_state>::alloc<bool>(bool) object_pool.hpp:114 (BoostAsioIssue:x86_64+0x100024951)  
    #3 boost::asio::detail::kqueue_reactor::allocate_descriptor_state() kqueue_reactor.ipp:524 (BoostAsioIssue:x86_64+0x100024817)  
    #4 boost::asio::detail::kqueue_reactor::register_descriptor(int, boost::asio::detail::kqueue_reactor::descriptor_state*&) kqueue_reactor.ipp:141 (BoostAsioIssue:x86_64+0x100024410)  
    #5 boost::asio::detail::reactive_socket_service_base::do_open(boost::asio::detail::reactive_socket_service_base::base_implementation_type&, int, int, int, boost::system::error_code&) reactive_socket_service_base.ipp:186 (BoostAsioIssue:x86_64+0x100023e90)  
    #6 boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>::open(boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>::implementation_type&, boost::asio::ip::udp const&, boost::system::error_code&) reactive_socket_service.hpp:127 (BoostAsioIssue:x86_64+0x1000239d8)  
    #7 boost::asio::basic_socket<boost::asio::ip::udp>::connect(boost::asio::ip::basic_endpoint<boost::asio::ip::udp> const&, boost::system::error_code&) basic_socket.hpp:801 (BoostAsioIssue:x86_64+0x10001a1b1)  
    #8 UdpTransport::connect(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short) main.cpp:52 (BoostAsioIssue:x86_64+0x100006fbe)  
    #9 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:25 (BoostAsioIssue:x86_64+0x10000679c)  
    #10 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:24 (BoostAsioIssue:x86_64+0x1000032b3)  
    #11 main main.cpp:95 (BoostAsioIssue:x86_64+0x10000265f)  
  
  Mutex M293 (0x7b40000002a8) created at:  
    #0 pthread_mutex_init <null>:2667088 (libclang_rt.tsan_osx_dynamic.dylib:x86_64h+0x29383)  
    #1 boost::asio::detail::posix_mutex::posix_mutex() posix_mutex.ipp:34 (BoostAsioIssue:x86_64+0x100007a69)  
    #2 boost::asio::detail::posix_mutex::posix_mutex() posix_mutex.ipp:33 (BoostAsioIssue:x86_64+0x100007a08)  
    #3 boost::asio::detail::conditionally_enabled_mutex::conditionally_enabled_mutex(bool) conditionally_enabled_mutex.hpp:108 (BoostAsioIssue:x86_64+0x10000d53a)  
    #4 boost::asio::detail::conditionally_enabled_mutex::conditionally_enabled_mutex(bool) conditionally_enabled_mutex.hpp:110 (BoostAsioIssue:x86_64+0x10000cebd)  
    #5 boost::asio::detail::kqueue_reactor::kqueue_reactor(boost::asio::execution_context&) kqueue_reactor.ipp:51 (BoostAsioIssue:x86_64+0x10000fcb8)  
    #6 boost::asio::detail::kqueue_reactor::kqueue_reactor(boost::asio::execution_context&) kqueue_reactor.ipp:52 (BoostAsioIssue:x86_64+0x10000faa8)  
    #7 boost::asio::execution_context::service* boost::asio::detail::service_registry::create<boost::asio::detail::kqueue_reactor, boost::asio::io_context>(void*) service_registry.hpp:87 (BoostAsioIssue:x86_64+0x10000f979)  
    #8 boost::asio::detail::service_registry::do_use_service(boost::asio::execution_context::service::key const&, boost::asio::execution_context::service* (*)(void*), void*) service_registry.ipp:132 (BoostAsioIssue:x86_64+0x10000ed95)  
    #9 boost::asio::detail::kqueue_reactor& boost::asio::detail::service_registry::use_service<boost::asio::detail::kqueue_reactor>(boost::asio::io_context&) service_registry.hpp:39 (BoostAsioIssue:x86_64+0x10000f8aa)  
    #10 boost::asio::detail::kqueue_reactor& boost::asio::use_service<boost::asio::detail::kqueue_reactor>(boost::asio::io_context&) io_context.hpp:39 (BoostAsioIssue:x86_64+0x10000f78c)  
    #11 boost::asio::detail::reactive_socket_service_base::reactive_socket_service_base(boost::asio::io_context&) reactive_socket_service_base.ipp:34 (BoostAsioIssue:x86_64+0x10000f28c)  
    #12 boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>::reactive_socket_service(boost::asio::io_context&) reactive_socket_service.hpp:79 (BoostAsioIssue:x86_64+0x10000f105)  
    #13 boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>::reactive_socket_service(boost::asio::io_context&) reactive_socket_service.hpp:80 (BoostAsioIssue:x86_64+0x10000f088)  
    #14 boost::asio::execution_context::service* boost::asio::detail::service_registry::create<boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>, boost::asio::io_context>(void*) service_registry.hpp:87 (BoostAsioIssue:x86_64+0x10000ebe9)  
    #15 boost::asio::detail::service_registry::do_use_service(boost::asio::execution_context::service::key const&, boost::asio::execution_context::service* (*)(void*), void*) service_registry.ipp:132 (BoostAsioIssue:x86_64+0x10000ed95)  
    #16 boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>& boost::asio::detail::service_registry::use_service<boost::asio::detail::reactive_socket_service<boost::asio::ip::udp> >(boost::asio::io_context&) service_registry.hpp:39 (BoostAsioIssue:x86_64+0x10000eb1a)  
    #17 boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>& boost::asio::use_service<boost::asio::detail::reactive_socket_service<boost::asio::ip::udp> >(boost::asio::io_context&) io_context.hpp:39 (BoostAsioIssue:x86_64+0x10000e99c)  
    #18 boost::asio::basic_io_object<boost::asio::detail::reactive_socket_service<boost::asio::ip::udp>, true>::basic_io_object(boost::asio::io_context&) basic_io_object.hpp:224 (BoostAsioIssue:x86_64+0x10000e8e6)  
    #19 boost::asio::basic_socket<boost::asio::ip::udp>::basic_socket(boost::asio::io_context&) basic_socket.hpp:94 (BoostAsioIssue:x86_64+0x10000e878)  
    #20 boost::asio::basic_datagram_socket<boost::asio::ip::udp>::basic_datagram_socket(boost::asio::io_context&) basic_datagram_socket.hpp:74 (BoostAsioIssue:x86_64+0x10000e818)  
    #21 boost::asio::basic_datagram_socket<boost::asio::ip::udp>::basic_datagram_socket(boost::asio::io_context&) basic_datagram_socket.hpp:75 (BoostAsioIssue:x86_64+0x1000068e8)  
    #22 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:17 (BoostAsioIssue:x86_64+0x100006738)  
    #23 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:24 (BoostAsioIssue:x86_64+0x1000032b3)  
    #24 main main.cpp:95 (BoostAsioIssue:x86_64+0x10000265f)  
  
  Thread T4 (tid=458732, running) created by main thread at:  
    #0 pthread_create <null>:2667280 (libclang_rt.tsan_osx_dynamic.dylib:x86_64h+0x283ed)  
    #1 std::__1::thread::thread<void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*, void>(void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*&&)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*&&) __threading_support:327 (BoostAsioIssue:x86_64+0x100018950)  
    #2 std::__1::thread::thread<void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*, void>(void (std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >::*&&)(), std::__1::__async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >*&&) thread:360 (BoostAsioIssue:x86_64+0x100014848)  
    #3 std::__1::__make_async_assoc_state<void, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()> >(std::__1::future<void>, std::__1::__async_func<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()>&&) future:2301 (BoostAsioIssue:x86_64+0x100013ce6)  
    #4 std::__1::async<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()>(std::__1::future<std::__1::__invoke_of<std::__1::decay<UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()>::type, std::__1::decay<>::type>::type>, std::__1::launch, UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>)::'lambda'()&&) future:2349 (BoostAsioIssue:x86_64+0x100006ae3)  
    #5 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:18 (BoostAsioIssue:x86_64+0x100006764)  
    #6 UdpTransport::UdpTransport(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned short, std::__1::function<void (std::__1::vector<unsigned char, std::__1::allocator<unsigned char> >)>) main.cpp:24 (BoostAsioIssue:x86_64+0x1000032b3)  
    #7 main main.cpp:95 (BoostAsioIssue:x86_64+0x10000265f)  
  
SUMMARY: ThreadSanitizer: data race conditionally_enabled_mutex.hpp:53 in boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&)  
==================  
```  
  
**Sample code:**  
```  
#include <iostream>  
#include <chrono>  
#include <boost/asio.hpp>  
  
class UdpTransport  
{  
public:  
    UdpTransport(const std::string& host, const uint16_t port, std::function<void(std::vector<uint8_t>)> receivedDataHandler)  
    : m_socket(m_service)  
    , m_serviceThreadFuture(std::async(std::launch::async, [this] {  
          
        boost::asio::io_service::work work(m_service);  
        m_service.run();  
    }))  
    , m_receivedDataHandler(std::move(receivedDataHandler))  
    {  
        connect(host, port);  
        receiveDataAsync();  
    }  
      
    ~UdpTransport()  
    {  
        m_service.stop();  
    }  
      
    void send(const std::vector<uint8_t>& dataToSend)  
    {  
        boost::system::error_code sendError = {};  
        const size_t sendSize = m_socket.send(boost::asio::buffer(dataToSend    ), 0, sendError);  
        if (sendSize != dataToSend.size() || sendError)  
        {  
            throw std::runtime_error("Send error: " + sendError.message());  
        }  
    }  
      
private:  
    void connect(const std::string& host, const uint16_t port)  
    {  
        boost::asio::ip::udp::resolver resolver(m_service);  
        boost::asio::ip::udp::resolver::query query(host, std::to_string(port));  
        boost::asio::ip::udp::resolver::iterator iter = resolver.resolve(query);  
          
        boost::system::error_code connectError = {};  
        m_socket.connect(*iter, connectError);  
        if (connectError)  
        {  
            throw std::runtime_error("Connection error: " + connectError.message());  
        }  
    }  
      
private:  
    void receiveDataAsync()  
    {  
        m_socket.async_receive(boost::asio::buffer(m_receiveBuffer), [this] (boost::system::error_code /* readError */, size_t readBytes) {  
            if (!readBytes)  
            {  
                // Error  
                return;  
            }  
              
            m_receivedDataHandler({ m_receiveBuffer.begin(), m_receiveBuffer.begin() + readBytes });  
              
            // Reinstall read handler  
            receiveDataAsync();  
        });  
    }  
      
private:  
    std::array<unsigned char, INT16_MAX> m_receiveBuffer;  
    boost::asio::io_service m_service;  
      
    boost::asio::ip::udp::socket m_socket;  
    std::future<void> m_serviceThreadFuture;  
      
    std::function<void(std::vector<uint8_t>)> m_receivedDataHandler;  
};  
  
std::chrono::milliseconds GetCurrentTime(void)  
{  
    return std::chrono::duration_cast<std::chrono::milliseconds>(std::chrono::system_clock::now().time_since_epoch());  
}  
  
int main(int argc, const char * argv[])  
{  
    // payload and host name couldn't be provided because they are confident.  
    const std::vector<uint8_t> payload = { ... };  
    UdpTransport transport("somehost.example.com", 53, [] (std::vector<uint8_t> data) {  
        std::cout << "Received data of size " + std::to_string(data.size()) + "\n";  
    });  
      
    try  
    {  
        transport.send(payload);  
    }  
    catch (const std::exception& ex)  
    {  
        std::cout << std::string("Error: ") + ex.what() + "\n";;  
    }  
      
    for (int i = 0; i < 10; i++)  
    {  
        try  
        {  
            transport.send(payload);  
        }  
        catch (const std::exception& ex)  
        {  
            std::cout << std::string("Error: ") + ex.what() + "\n";;  
        }  
          
        sleep(1);  
    }  
      
    return 0;  
}  
```

---

## Comment 1

> Username: ruipacheco  
> Created at: 2019-02-21 14:31:05 UTC  
> Url: https://github.com/boostorg/asio/issues/149#issuecomment-466020300  

Maybe report this in the asio repo? https://github.com/chriskohlhoff/asio

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:53:26 UTC  
> Url: https://github.com/boostorg/asio/issues/149#issuecomment-752289950  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#641](https://github.com/chriskohlhoff/asio/issues/641).
