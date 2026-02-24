# #397 - asio event loop stuck infinitly when signal(SIGABRT, SEGV,...) is raised while executing another task [Open]

> Username: GowrishankarSG05  
> Created at: 2022-10-04 09:18:25 UTC  
> Updated at: 2022-12-08 03:50:04 UTC  
> Url: https://github.com/boostorg/asio/issues/397  

Hello team,  
  
I have come across the below issue while developing my application using boost asio event loop with the combination of asio::posix::stream_descriptor and boost::asio::signal_set.  
  
1. Created asio::io_service  
2. Created stream_descriptor to monitor posix file descriptor events(read & error) and passed io_service to execute fd eventhandler on the context of io_service.  
3. Created signal_set and added SIGTERM, SIGINIT, SIGABRT, SIGSEGV, ... to handle program termination on the context of io_service.  
4. At first, there was an event received in the monitoring fd. Then corresponding eventHandler called  
    4.1 Segmentation fault occurred within the eventHandler due to invalid memory access.  
    4.2 Now interestingly the program execution was stuck at the same place where sigsegv occurred and signal handler was not called for the SIGSEGV.  
  
In short, Whenever signal is raised when the event loop executer is in middle of processing some event, the signal handler is not getting called at all and stuck at the same place.  
  
**GDB snippet:**  
-------------------  
Thread 1 "boostAsioExampl" hit Breakpoint 1, PollNativeFdWithBoostAsio::waitHandler (this=0x55555555ad50 <_start>, error=...) at boostAsioExample.cpp:40  
40	    void waitHandler(const boost::system::error_code &error) {  **<== This is my handler for file descriptor events**  
(gdb) n  
41	        std::cout << "\nwait handler\n";  
(gdb) n  
  
wait handler  
42	        std::cout << "Making SIGSEGV...\n";  
(gdb) n  
Making SIGSEGV...  
43	        char *ptr = nullptr;  
(gdb) n  
44	        *ptr = '1'; **<== Explicitly making segmentation fault by accessing nullptr**  
(gdb) n  
  
Thread 1 "boostAsioExampl" received signal SIGSEGV, Segmentation fault.  
0x0000555555565dc9 in PollNativeFdWithBoostAsio::waitHandler (this=0x7fffffffe310, error=...) at boostAsioExample.cpp:44  
44	        *ptr = '1';  
(gdb) n  
boost::asio::detail::boost_asio_signal_handler (signal_number=0) at /usr/include/boost/asio/detail/impl/signal_set_service.ipp:68  
68	{ **<== signal_set handler called**  
(gdb) n  
76	  int saved_errno = errno;  
(gdb) n  
77	  signal_state* state = get_signal_state();  
(gdb) n  
78	  signed_size_type result = ::write(state->write_descriptor_,  
(gdb) n  
81	  errno = saved_errno;  
(gdb) n  
89	}  
(gdb) n  
  
Thread 1 "boostAsioExampl" received signal SIGSEGV, Segmentation fault.  
0x0000555555565dc9 in PollNativeFdWithBoostAsio::waitHandler (this=0x7fffffffe310, error=...) at boostAsioExample.cpp:44  
44	        *ptr = '1'; **<== Again the execution has come into the same place where segv happened. This continues infinitely**  
(gdb) n  
boost::asio::detail::boost_asio_signal_handler (signal_number=11) at /usr/include/boost/asio/detail/impl/signal_set_service.ipp:68  
68	{  
(gdb) n  
76	  int saved_errno = errno;  
(gdb) n  
77	  signal_state* state = get_signal_state();  
(gdb) n  
78	  signed_size_type result = ::write(state->write_descriptor_,  
(gdb) n  
81	  errno = saved_errno;  
(gdb) n  
89	}  
(gdb) n  
  
Thread 1 "boostAsioExampl" received signal SIGSEGV, Segmentation fault.  
0x0000555555565dc9 in PollNativeFdWithBoostAsio::waitHandler (this=0x7fffffffe310, error=...) at boostAsioExample.cpp:44  
44	        *ptr = '1';  
  
  
Kindly let me know if anymore information is required from my side.  
Thanks in advance!

---

## Comment 1

> Username: GowrishankarSG05  
> Created at: 2022-10-26 08:41:15 UTC  
> Updated at: 2022-11-06 18:29:56 UTC  
> Url: https://github.com/boostorg/asio/issues/397#issuecomment-1291697257  

I just dug into the code around boost asio and noticed the root cause of this issue.  
  
Steps to create this issue:  
1. Register for system signal(SIGSEGV, SIGABRT, ...) using signal_set  
2. Run the event loop using io_service::run(). io_service here it has only one job that is to monitor watch signals.  
3. Now post a job to io_service  
4. While the job is being executed, Deliberately make SIGSEGV by simple accessing nullptr ;)  
5. This is the starting point where event loop start to stuck in an infinite loop  
  
Order of the execution:  
1. When SIGSEGV is raised while executing a job, the program execution moves to boost_asio_signal_handler  
2. boost_asio_signal_handler simple writes into a fd which is what being monitored in the other side, I assume.  
3. Then hereafter boost_asio_signal_handler just leaves the function  
4. Now the execution control moves to the place where SIGSEGV risen.  
5. In this use case, Sadly epoll_wait will never have a chance to notify about the data written by boost_asio_signal_handler.  
6. Step 1 to 5 repeats infinitely.
