# #143 - Segmentation violation while destroying io_context [Closed]

> Username: igorpsoares  
> Created at: 2018-09-04 19:18:35 UTC  
> Updated at: 2020-12-30 00:53:01 UTC  
> Closed at: 2020-12-30 00:53:00 UTC  
> Url: https://github.com/boostorg/asio/issues/143  

Hi, I am facing a segmentation violation on the destructor of a class that makes use of io_service. I am not sure whether this a bug within asio or if this is due to some misuse. I will be glad if someone have some advice on this.  
  
Here is the exception Stack Trace:  
```  
Received signal: 11 - SIGSEGV. Segmentation violation  
    Thread: AsyncHttp-165596 - 140612727699200  
    Stack trace:  
    /ArbeitClick/ACServer/scserver: boost::asio::detail::timer_queue_set::get_all_timers(boost::asio::detail::op_queue<boost::asio::detail::scheduler_operation>&)()+0x21  
    /ArbeitClick/ACServer/scserver: boost::asio::detail::epoll_reactor::shutdown()()+0xdb  
    /ArbeitClick/ACServer/scserver: boost::asio::detail::service_registry::shutdown_services()()+0x31  
    /ArbeitClick/ACServer/scserver: boost::asio::execution_context::shutdown()()+0x1b  
    /ArbeitClick/ACServer/scserver: boost::asio::execution_context::~execution_context()()+0x19  
    /ArbeitClick/ACServer/scserver: boost::asio::io_context::~io_context()()+0x18  
    /ArbeitClick/ACServer/scserver: arbeit::httpclient::HttpClient::~HttpClient()()+0x14d  
```  
  
The io service is defined on my header file like this:  
`boost::asio::io_service ios;`  
  
On a method within the HttpClient class I start the thread like this:  
`httpThread = std::thread(boost::bind(&boost::asio::io_service::run, &ios));`  
  
In order to make sure I can destroy the HttpClient instance we call a method that checks if the ios service is stopped, which is the following:  
```  
bool HttpClient::isStopped()  
{  
      return ios.stopped();  
}  
```  
  
If the method above returns true I try to destroy the class instance. Here is the destructor:  
```  
HttpClient::~HttpClient()  
{  
	if (httpThread.joinable())  
		httpThread.join();	//Waits until the service has stopped  
}  
```  
  
The exception is raised once in a while. It is not raised every time I call the destructor, so I am not really sure what could be causing the segmentation violation.

---

## Comment 1

> Username: igorpsoares  
> Created at: 2018-10-02 18:38:52 UTC  
> Url: https://github.com/boostorg/asio/issues/143#issuecomment-426385104  

I have updated to boost 1.68 and got a very similar stack trace:  
  
```  
Received signal: 11 - SIGSEGV. Segmentation violation  
        Thread: A-Http-17603 - 140517723576064  
        Stack trace:  
          /ArbeitClick/ACServer/scserver: arbeit::SignalHandler::getSignalInformation(int)()+0x191  
          /ArbeitClick/ACServer/scserver() [0x144a973]  
          /lib64/libc.so.6: ()+0x35250  
          /ArbeitClick/ACServer/scserver: boost::asio::detail::timer_queue_set::get_all_timers(boost::asio::detail::op_queue<boost::asio::detail::scheduler_operation>&)()+0x21  
          /ArbeitClick/ACServer/scserver: boost::asio::detail::epoll_reactor::shutdown()()+0xdb  
          /ArbeitClick/ACServer/scserver: boost::asio::detail::service_registry::shutdown_services()()+0x31  
          /ArbeitClick/ACServer/scserver: boost::asio::execution_context::shutdown()()+0x1b  
          /ArbeitClick/ACServer/scserver: boost::asio::execution_context::~execution_context()()+0x19  
          /ArbeitClick/ACServer/scserver: boost::asio::io_context::~io_context()()+0x18  
          /ArbeitClick/ACServer/scserver: arbeit::httpclient::HttpClient::~HttpClient()()+0x227  
```

---

## Comment 2

> Username: HeikoBoettger  
> Created at: 2019-03-18 16:51:14 UTC  
> Url: https://github.com/boostorg/asio/issues/143#issuecomment-474002783  

I believe I found a related issue since the problem in our code also starts when the io_context is destructed:  
  
```  
#0 0x453bcd in std::__atomic_base<long>::operator--() /usr/include/c++/6/bits/atomic_base.h:304  
    #1 0x453bcd in boost::asio::detail::scheduler::work_finished() ../../../../../fs/fsdev/usr/include/boost/asio/detail/scheduler.hpp:94  
    #2 0x4b8bdf in boost::asio::io_context::executor_type::on_work_finished() const ../../../../../fs/fsdev/usr/include/boost/asio/impl/io_context.hpp:227  
    #3 0x4b8bdf in boost::asio::detail::handler_work<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffer, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::beast::basic_flat_buffer<std::allocator<char> >, std::function<void (boost::system::error_code const&, unsigned long)> > >, boost::asio::io_context::executor_type>::~handler_work() ../../../../../fs/fsdev/usr/include/boost/asio/detail/handler_work.hpp:49  
    #4 0x4b8bdf in boost::asio::detail::reactive_socket_recv_op<std::array<boost::asio::mutable_buffer, 2ul>, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_some_op<boost::asio::mutable_buffer, boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, true>::read_op<boost::beast::basic_flat_buffer<std::allocator<char> >, std::function<void (boost::system::error_code const&, unsigned long)> > > >::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) ../../../../../fs/fsdev/usr/include/boost/asio/detail/reactive_socket_recv_op.hpp:102  
    #5 0x4264e4 in boost::asio::detail::scheduler_operation::destroy() ../../../../../fs/fsdev/usr/include/boost/asio/detail/scheduler_operation.hpp:45  
    #6 0x4264e4 in boost::asio::detail::scheduler::shutdown() ../../../../../fs/fsdev/usr/include/boost/asio/detail/impl/scheduler.ipp:120  
    #7 0x42e30c in boost::asio::detail::service_registry::shutdown_services() ../../../../../fs/fsdev/usr/include/boost/asio/detail/impl/service_registry.ipp:44  
    #8 0x42e30c in boost::asio::execution_context::shutdown() ../../../../../fs/fsdev/usr/include/boost/asio/impl/execution_context.ipp:41  
    #9 0x42e30c in boost::asio::execution_context::~execution_context() ../../../../../fs/fsdev/usr/include/boost/asio/impl/execution_context.ipp:34  
    #10 0x44375e in boost::asio::io_context::~io_context() ../../../../../fs/fsdev/usr/include/boost/asio/impl/io_context.ipp:55  
    #11 0x44375e in test::BeastWebSocketAsyncTransportTest_DeadConnection_Test::TestBody() ../../../runtime/cxx/test/src/test/asio/BeastWebSocketAsyncTransportTest.cpp:124  
    #12 0x7fb4f504e70d in void testing::internal::HandleSehExceptionsInMethodIfSupported<testing::Test, void>(testing::Test*, void (testing::Test::*)(), char const*) ../src/googletest/src/gtest.cc:2402  
    #13 0x7fb4f504e70d in void testing::internal::HandleExceptionsInMethodIfSupported<testing::Test, void>(testing::Test*, void (testing::Test::*)(), char const*) ../src/googletest/src/gtest.cc:2438  
    #14 0x7fb4f5038acd in testing::Test::Run() ../src/googletest/src/gtest.cc:2475  
```  
The problem here is that the io_context is going out of scope while there is still a service (the scheduler/io_context_impl). When io_context calls the destructor of it's base (execution_context) calls its shutdown-method which shuts down the service_registry. Now the registry tries to terminate the service and causing the execution of code on the deleted scheduler/io_context.  
  
The overall problem is that the impl_type passed to `add_service` that contains a pointer into io_context which isn't owned.  
  
One solution I have found is calling shutdown (which is thanks good not virtual) from the destructor in io_context what is exactly what was done in the destructor of executor_context (for the same reason?)  
  
A better approach might be to somehow unregisters the service added in the constructor of io_context by doing the opposite of `add_impl`.

---

## Comment 3

> Username: djarek  
> Created at: 2019-03-18 21:20:39 UTC  
> Url: https://github.com/boostorg/asio/issues/143#issuecomment-474105855  

@HeikoBoettger Are you using Boost 1.69? It looks like you ran into an async operation leak in `websocket::stream` reported in https://github.com/boostorg/beast/issues/1357. You should check with current master, it should be fixed now.

---

## Comment 4

> Username: djarek  
> Created at: 2019-03-18 21:25:50 UTC  
> Updated at: 2019-03-18 21:27:20 UTC  
> Url: https://github.com/boostorg/asio/issues/143#issuecomment-474107639  

On a second look, this might not be the same issue. How do you know that the scheduler is already destroyed?  `~execution_context()` first calls `shutdown()` on all services, so during the `shutdown` step, all services should still be valid:   
https://github.com/boostorg/asio/blob/develop/include/boost/asio/impl/execution_context.ipp#L32

---

## Comment 5

> Username: HeikoBoettger  
> Created at: 2019-03-19 07:35:53 UTC  
> Url: https://github.com/boostorg/asio/issues/143#issuecomment-474229729  

@djarek According to the stacktrace the decrement of atomic_count causes a segmentation fault which is a member of the scheduler, so I assumed from the stack trace the schedule is invalid. The problem already starts when invoking #2 the on_work_finished on scheduler_type which uses it's member io_context_ which is  reference to the delete io_context from which it uses the _impl (a reference to the scheduler). Since io_context is deleted _impl could point to anything.  
  
I would have better said the code executes scheduler-code via an invalid reference.

---

## Comment 6

> Username: HeikoBoettger  
> Created at: 2019-03-19 07:42:19 UTC  
> Url: https://github.com/boostorg/asio/issues/143#issuecomment-474231299  

@djarek sorry forgot to mention I am using boost 1.67.0. What do you mean by leak, the address sanitizer, do you mean a dangling pointer is being used? The address sanitizer clearly says its a segmentation fault.

---

## Comment 7

> Username: HeikoBoettger  
> Created at: 2019-03-19 14:15:45 UTC  
> Url: https://github.com/boostorg/asio/issues/143#issuecomment-474389559  

@djarek I found a bug in our code as well, we are spinning a loop in a tcp-client-thread  
```  
while(io_service.stopped()) io_service.run();  
```  
From another thread we are calling ::stop() which causes the loop to terminate and the thread to terminate. The thread destroys the it's socket but doesn't cancel/remove the reactive_socket_recv_op on the socket. Now since main-thread owns to io_context to calls the destructor leading to the chain above. I believe this is wrong since the methods called in my stack tract should also be processed by the client- thread. I moved the io_service creation into the client-thread (which is also spinning the loop) so that the stop-method is the only one ever called by the main-thread. That should solve some of the problems.  
  
However what makes me wonder, shouldn't the boost::beast::websocket::streams destructor cancel the receive operation? I can only guess that this has to do with stopping to spin the loop and cancellation cannot be performed before the destructor.  
  
Anyway I am pretty sure that when destroying io_context the on_work_finshed-method in the stack trace I have posted shouldn't use the io_context any more.  
  
In master the this-pointer is passed to impl_type:  
https://github.com/boostorg/asio/blob/54cdc73c29550e54d67c7679880307f04f258543/include/boost/asio/impl/io_context.ipp#L44  
  
And it looks like there is still the same method as in 1.67.0 referring the io_context:  
https://github.com/boostorg/asio/blob/54cdc73c29550e54d67c7679880307f04f258543/include/boost/asio/impl/io_context.hpp#L249  
  
For me it looks strange accessing a method from the io_context after it's destructor has been finished.  
  
Are there any plans to add some assertions to be optionally compiled, making sure to not call io_context-methods designed to be executed by a single thread?

---

## Comment 8

> Username: ghost  
> Created at: 2020-12-30 00:52:59 UTC  
> Url: https://github.com/boostorg/asio/issues/143#issuecomment-752289862  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#637](https://github.com/chriskohlhoff/asio/issues/637).
