# #248 - Boost v1.67 Asio compile error for QNX700 [Closed]

> Username: Sadrieh  
> Created at: 2018-10-17 04:20:57 UTC  
> Updated at: 2019-10-28 19:16:56 UTC  
> Closed at: 2019-10-28 19:16:55 UTC  
> Url: https://github.com/boostorg/asio/issues/248  

Hi,   
I am trying to build and use boost libraries for QNX700, but I cannot compile a test app for asio library.   
Following is how I have ported the version 1.67.0:  
  
Three files needs to be modified:  
    boost_1_67_0/boost/stacktrace/detail/location_from_symbol.hpp Line 31:  
  
   `#if !defined(__QNX__)  
            if (!::dladdr(addr, &dli_))  
    #else  
            if (!::dladdr(const_cast<void*>(addr), &dli_))  
    #endif`  
boost_1_67_0/boost/stacktrace/detail/frame_unwind.ipp Line 87:  
  
    `#if !defined(__QNX__)  
        const bool dl_ok = !!::dladdr(addr_, &dli);  
    #else  
        const bool dl_ok = !!::dladdr(const_cast<void*>(addr_), &dli);  
    #endif`  
  
    boost_1_67_0/boost/libs/filesystem/src/operations.cpp Line 1457 add:  
  
          `&& !(defined(__QNX__) || defined(__QNXNTO__)) \`  
and how I have build it:  
`./b2 --layout=versioned --build-type=complete target-os=qnx toolset=qcc threadapi=pthread stage`  
  
The test App:  
`#include <string>  
`#include <iostream>  
`  
`#include <boost/asio.hpp>  
`#include <boost/bind.hpp>  
`#include <boost/thread.hpp>  
`  
`class timer  
`{  
`public:  
`    timer(boost::asio::io_service &io):  
`    m_timer(io),  
`    counter(0)  
`    {  
`        timeout();  
`    }  
`private:  
`    boost::asio::deadline_timer m_timer;  
`    unsigned int counter;  
`    void timeout()  
`    {  
`        std::cout<<boost::this_thread::get_id()<<":Timer:"<<counter++<<std::endl;          
`        m_timer.expires_from_now(boost::posix_time::seconds(1));  
`        m_timer.async_wait(boost::bind(&timer::timeout,this));  
`    }  
`};  
`int main( int argc, char* argv[] )  
`{  
`    boost::asio::io_service io_service;  
`    timer t(io_service);  
`    io_service.run();  
`    return 1;      
`}  
  
and build the command:  
`qcc src/main.cpp -o test.bin -I ../../boost_1_67 -L../../boost_1_67/stage/lib/ -lboost_system-qcc-mt-d-x64-1_67 -lboost_thread-qcc-mt-d-x64-1_67  
  
and the error I get is:  
  
>In file included from ../../boost_1_67/boost/asio/detail/executor_op.hpp:20:0,  
                 from ../../boost_1_67/boost/asio/impl/system_executor.hpp:18,  
                 from ../../boost_1_67/boost/asio/system_executor.hpp:129,  
                 from ../../boost_1_67/boost/asio/associated_executor.hpp:21,  
                 from ../../boost_1_67/boost/asio.hpp:21,  
                 from src/main.cpp:4:  
../../boost_1_67/boost/asio/detail/wait_handler.hpp: In instantiation of 'static boost::asio::detail::wait_handler<Handler>* boost::asio::detail::wait_handler<Handler>::ptr::allocate(Handler&) [with Handler = boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >]':  
../../boost_1_67/boost/asio/detail/deadline_timer_service.hpp:235:24:   required from 'void boost::asio::detail::deadline_timer_service<Time_Traits>::async_wait(boost::asio::detail::deadline_timer_service<Time_Traits>::implementation_type&, Handler&) [with Handler = boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >; Time_Traits = boost::asio::time_traits<boost::posix_time::ptime>]'  
../../boost_1_67/boost/asio/basic_deadline_timer.hpp:610:5:   required from 'typename boost::asio::async_result<typename std::__1::decay<_Tp>::type, void(boost::system::error_code)>::return_type boost::asio::basic_deadline_timer<Time, TimeTraits>::async_wait(WaitHandler&&) [with WaitHandler = boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >; Time = boost::posix_time::ptime; TimeTraits = boost::asio::time_traits<boost::posix_time::ptime>; typename boost::asio::async_result<typename std::__1::decay<_Tp>::type, void(boost::system::error_code)>::return_type = void]'  
src/main.cpp:24:61:   required from here  
../../boost_1_67/boost/asio/detail/handler_alloc_helpers.hpp:170:75: error: no matching function for call to 'std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >::rebind_alloc(boost::asio::detail::get_hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, std::__1::allocator<void> >::type)'  
                 handler, ::boost::asio::get_associated_allocator(handler))); \  
                                                                           ^  
../../boost_1_67/boost/asio/detail/wait_handler.hpp:36:3: note: in expansion of macro 'BOOST_ASIO_DEFINE_HANDLER_PTR'  
   BOOST_ASIO_DEFINE_HANDLER_PTR(wait_handler);  
   ^  
In file included from /home/afs/qnx700/target/qnx7/usr/include/c++/v1/algorithm:628:0,  
                 from /home/afs/qnx700/target/qnx7/usr/include/c++/v1/string:439,  
                 from src/main.cpp:1:  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note: candidate: constexpr std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >::rebind_alloc()  
     template <class _Tp> struct rebind_alloc  
                                 ^  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note:   candidate expects 0 arguments, 1 provided  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note: candidate: constexpr std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >::rebind_alloc(const std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >&)  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note:   no known conversion for argument 1 from 'boost::asio::detail::get_hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, std::__1::allocator<void> >::type {aka boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void>}' to 'const std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >&'  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note: candidate: constexpr std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >::rebind_alloc(std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >&&)  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note:   no known conversion for argument 1 from 'boost::asio::detail::get_hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, std::__1::allocator<void> >::type {aka boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void>}' to 'std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >&&'  
In file included from ../../boost_1_67/boost/asio/detail/executor_op.hpp:20:0,  
                 from ../../boost_1_67/boost/asio/impl/system_executor.hpp:18,  
                 from ../../boost_1_67/boost/asio/system_executor.hpp:129,  
                 from ../../boost_1_67/boost/asio/associated_executor.hpp:21,  
                 from ../../boost_1_67/boost/asio.hpp:21,  
                 from src/main.cpp:4:  
../../boost_1_67/boost/asio/detail/handler_alloc_helpers.hpp:171:26: error: 'struct std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >' has no member named 'allocate'  
       return a.allocate(1); \  
                          ^  
../../boost_1_67/boost/asio/detail/wait_handler.hpp:36:3: note: in expansion of macro 'BOOST_ASIO_DEFINE_HANDLER_PTR'  
   BOOST_ASIO_DEFINE_HANDLER_PTR(wait_handler);  
   ^  
../../boost_1_67/boost/asio/detail/wait_handler.hpp: In instantiation of 'void boost::asio::detail::wait_handler<Handler>::ptr::reset() [with Handler = boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >]':  
../../boost_1_67/boost/asio/detail/wait_handler.hpp:36:3:   required from 'boost::asio::detail::wait_handler<Handler>::ptr::~ptr() [with Handler = boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >]'  
../../boost_1_67/boost/asio/detail/deadline_timer_service.hpp:235:37:   required from 'void boost::asio::detail::deadline_timer_service<Time_Traits>::async_wait(boost::asio::detail::deadline_timer_service<Time_Traits>::implementation_type&, Handler&) [with Handler = boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >; Time_Traits = boost::asio::time_traits<boost::posix_time::ptime>]'  
../../boost_1_67/boost/asio/basic_deadline_timer.hpp:610:5:   required from 'typename boost::asio::async_result<typename std::__1::decay<_Tp>::type, void(boost::system::error_code)>::return_type boost::asio::basic_deadline_timer<Time, TimeTraits>::async_wait(WaitHandler&&) [with WaitHandler = boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >; Time = boost::posix_time::ptime; TimeTraits = boost::asio::time_traits<boost::posix_time::ptime>; typename boost::asio::async_result<typename std::__1::decay<_Tp>::type, void(boost::system::error_code)>::return_type = void]'  
src/main.cpp:24:61:   required from here  
../../boost_1_67/boost/asio/detail/handler_alloc_helpers.hpp:189:67: error: no matching function for call to 'std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >::rebind_alloc(boost::asio::detail::get_hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, std::__1::allocator<void> >::type)'  
                   *h, ::boost::asio::get_associated_allocator(*h))); \  
                                                                   ^  
../../boost_1_67/boost/asio/detail/wait_handler.hpp:36:3: note: in expansion of macro 'BOOST_ASIO_DEFINE_HANDLER_PTR'  
   BOOST_ASIO_DEFINE_HANDLER_PTR(wait_handler);  
   ^  
In file included from /home/afs/qnx700/target/qnx7/usr/include/c++/v1/algorithm:628:0,  
                 from /home/afs/qnx700/target/qnx7/usr/include/c++/v1/string:439,  
                 from src/main.cpp:1:  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note: candidate: constexpr std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >::rebind_alloc()  
     template <class _Tp> struct rebind_alloc  
                                 ^  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note:   candidate expects 0 arguments, 1 provided  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note: candidate: constexpr std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >::rebind_alloc(const std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >&)  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note:   no known conversion for argument 1 from 'boost::asio::detail::get_hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, std::__1::allocator<void> >::type {aka boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void>}' to 'const std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >&'  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note: candidate: constexpr std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >::rebind_alloc(std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >&&)  
/home/afs/qnx700/target/qnx7/usr/include/c++/v1/memory:1472:33: note:   no known conversion for argument 1 from 'boost::asio::detail::get_hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, std::__1::allocator<void> >::type {aka boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void>}' to 'std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >&&'  
In file included from ../../boost_1_67/boost/asio/detail/executor_op.hpp:20:0,  
                 from ../../boost_1_67/boost/asio/impl/system_executor.hpp:18,  
                 from ../../boost_1_67/boost/asio/system_executor.hpp:129,  
                 from ../../boost_1_67/boost/asio/associated_executor.hpp:21,  
                 from ../../boost_1_67/boost/asio.hpp:21,  
                 from src/main.cpp:4:  
../../boost_1_67/boost/asio/detail/handler_alloc_helpers.hpp:190:9: error: 'struct std::__1::allocator_traits<boost::asio::detail::hook_allocator<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > >, void> >::rebind_alloc<boost::asio::detail::wait_handler<boost::_bi::bind_t<void, boost::_mfi::mf0<void, timer>, boost::_bi::list1<boost::_bi::value<timer*> > > > >' has no member named 'deallocate'  
         a.deallocate(static_cast<op*>(v), 1); \  
         ^  
../../boost_1_67/boost/asio/detail/wait_handler.hpp:36:3: note: in expansion of macro 'BOOST_ASIO_DEFINE_HANDLER_PTR'  
   BOOST_ASIO_DEFINE_HANDLER_PTR(wait_handler);  
   ^  
cc: /home/afs/qnx700/host/linux/x86_64/usr/lib/gcc/x86_64-pc-nto-qnx7.0.0/5.4.0/cc1plus error 1  
  
  
Any help provided is highly appreciated.  
  
Regards,  
Afshin

---

## Comment 1

> Username: mclow  
> Created at: 2018-10-20 15:05:16 UTC  
> Url: https://github.com/boostorg/asio/issues/248#issuecomment-431589413  

You'll get more attention from the Boost.ASIO maintainers if you post this on the ASIO subproject here: https://github.com/boostorg/asio or on the mailing list at boost-users@lists.boost.org.

---

## Comment 2

> Username: btaczala  
> Created at: 2018-11-05 09:44:27 UTC  
> Url: https://github.com/boostorg/asio/issues/248#issuecomment-435813505  

In my setup undefining _LIBCPP_HAS_NO_TEMPLATE_ALIASES helped!  
@Sadrieh can you check this?

---

## Comment 3

> Username: darbee  
> Created at: 2018-11-08 06:42:22 UTC  
> Url: https://github.com/boostorg/asio/issues/248#issuecomment-436889838  

I meet the same problem when  cross compile fast rtps for qnx!!

---

## Comment 4

> Username: btaczala  
> Created at: 2018-11-08 18:38:39 UTC  
> Url: https://github.com/boostorg/asio/issues/248#issuecomment-437109286  

@darbee I've checked vanilla LLVM headers and they follow the same logic, meaning that _LIBCPP_HAS_NO_TEMPLATE_ALIASES is defined for GCC compiler. I have no idea why that's the case.

---

## Comment 5

> Username: darbee  
> Created at: 2018-11-14 06:59:13 UTC  
> Url: https://github.com/boostorg/asio/issues/248#issuecomment-438558674  

hi  w0land @w0land , when I and -stdlib=libstdc++ flag when compling, It works. I learn that this flag will stop compiler from using llvm (qnx7/usr/include/c++/v1/). I do not know why LLVM has these problem

---

## Comment 6

> Username: Johnnyxy  
> Created at: 2019-06-03 09:27:09 UTC  
> Updated at: 2019-06-18 16:45:27 UTC  
> Url: https://github.com/boostorg/asio/issues/248#issuecomment-498182528  

I have the same problem with QNX SDP 7 (q++ 5.4.0) and Boost 1.69.0, 1.70.0 (currently available version).   
  
To be clear the following is not related to boost ```asio``` alone.  
Building boost with ```log``` and ```system``` modules is enough to trigger the compile errors.  
  
the error  
----------  
The error is a combination of code and compiler configuration (through compiler header files).  
Boost uses a feature that gets disabled when using the LLVM C++ library what results in the compiler errors.  
Actually when using a QCC target that uses the GNU C++ library ```gpp``` everything works fine only with the LLVM C++ library ```cxx``` this error occurs.  
  
For additional informations and instructions on how to solve this issue see here: https://github.com/boostorg/config/issues/288

---

## Comment 7

> Username: jzmaddock  
> Created at: 2019-10-28 19:16:55 UTC  
> Url: https://github.com/boostorg/asio/issues/248#issuecomment-547104150  

This is not a Boost.Config issue: Boost.Config already has:  
```  
#if defined(_LIBCPP_HAS_NO_TEMPLATE_ALIASES)  
#    define BOOST_NO_CXX11_ALLOCATOR  
#    define BOOST_NO_CXX11_POINTER_TRAITS  
#endif  
```  
Which would result in BOOST_NO_CXX11_ALLOCATOR being defined for this compiler.  The issue is that Asio is not using Boost.Config and is not detecting this use case.
