# #131 - Race condition between core->flush() and startup of feeding thread for asynchronous sink [Closed]

> Username: boldbyteboss  
> Created at: 2020-09-23 15:27:47 UTC  
> Updated at: 2020-09-27 16:52:05 UTC  
> Closed at: 2020-09-27 16:52:05 UTC  
> Url: https://github.com/boostorg/log/issues/131  

There's a race condition between the core->flush() operation and the startup of the feeding thread for asynchronous sinks that can result in the program aborting with an unhandled exception from the feeding thread. It is possible for core->flush() to begin doing the work of feeding records through an asynchronous sink before the sink's feeding thread has a chance to fully enter the running state. This results in an exception from asynchronous_sink::run() when the feeding thread discovers that there is already another thread (the thread calling flush()) servicing the sink.  
  
The attached reproduction example makes this race more reliable by inserting a slight delay into the background feeding thread. In out application, however, there is no such artificial delay and the race condition is exposed simply by system load and differing scheduling of the thread calling flush() and the background feeding thread in an asynchronous sink.  
  
Here's the call stack where the exception is thrown when the background feeding thread tries to start:  
  
```  
        vcruntime140.dll!_CxxThrowException(void * pExceptionObject, const _s__ThrowInfo * pThrowInfo) Line 75	C++  
 	boost_log-vc141-mt-x64-1_70.dll!boost::throw_exception<boost::exception_detail::error_info_injector<boost::log::v2_mt_nt6::unexpected_call> >(const boost::exception_detail::error_info_injector<boost::log::v2_mt_nt6::unexpected_call> & e) Line 70	C++  
 	boost_log-vc141-mt-x64-1_70.dll!boost::log::v2_mt_nt6::unexpected_call::throw_(const char * file, unsigned __int64 line, const char * descr) Line 548	C++  
 	flush.exe!boost::log::v2_mt_nt6::sinks::asynchronous_sink<boost::log::v2_mt_nt6::sinks::text_file_backend,boost::log::v2_mt_nt6::sinks::unbounded_fifo_queue>::scoped_thread_id::scoped_thread_id(boost::log::v2_mt_nt6::aux::light_rw_mutex & mut, boost::condition_variable_any & cond, boost::thread::id & tid, boost::atomics::atomic<bool> & sr) Line 185	C++  
>	flush.exe!boost::log::v2_mt_nt6::sinks::asynchronous_sink<boost::log::v2_mt_nt6::sinks::text_file_backend,boost::log::v2_mt_nt6::sinks::unbounded_fifo_queue>::run() Line 404	C++  
 	flush.exe!main::__l2::<lambda>() Line 26	C++  
```  
  
  
And here's the program main thread, which is already deep inside the flush operation:  
  
```  
	[Inline Frame] boost_log-vc141-mt-x64-1_70.dll!std::basic_filebuf<char,std::char_traits<char> >::open(const wchar_t *) Line 315	C++  
 	[Inline Frame] boost_log-vc141-mt-x64-1_70.dll!std::basic_ofstream<char,std::char_traits<char> >::open(const wchar_t *) Line 1187	C++  
 	[Inline Frame] boost_log-vc141-mt-x64-1_70.dll!boost::filesystem::basic_ofstream<char,std::char_traits<char> >::open(const boost::filesystem::path & mode, int) Line 130	C++  
 	boost_log-vc141-mt-x64-1_70.dll!boost::log::v2_mt_nt6::sinks::text_file_backend::consume(const boost::log::v2_mt_nt6::record_view & rec, const std::basic_string<char,std::char_traits<char>,std::allocator<char> > & formatted_message) Line 1372	C++  
 	flush.exe!boost::log::v2_mt_nt6::sinks::basic_formatting_sink_frontend<char>::feed_record<boost::recursive_mutex,boost::log::v2_mt_nt6::sinks::text_file_backend>(const boost::log::v2_mt_nt6::record_view & rec, boost::recursive_mutex & backend_mutex, boost::log::v2_mt_nt6::sinks::text_file_backend & backend) Line 499	C++  
 	flush.exe!boost::log::v2_mt_nt6::sinks::asynchronous_sink<boost::log::v2_mt_nt6::sinks::text_file_backend,boost::log::v2_mt_nt6::sinks::unbounded_fifo_queue>::do_feed_records() Line 525	C++  
 	flush.exe!boost::log::v2_mt_nt6::sinks::asynchronous_sink<boost::log::v2_mt_nt6::sinks::text_file_backend,  
  
boost::log::v2_mt_nt6::sinks::unbounded_fifo_queue>::flush() Line 500	C++  
 	boost_log-vc141-mt-x64-1_70.dll!boost::log::v2_mt_nt6::core::flush() Line 630	C++  
 	flush.exe!main() Line 30	C++  
  
```  
  
And here's the code for the reproduction:  
  
```  
#include <boost/log/core/core.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/sources/record_ostream.hpp>  
#include <boost/log/sinks/async_frontend.hpp>  
#include <boost/log/sinks/text_file_backend.hpp>  
  
#include <chrono>  
#include <iostream>  
#include <string>  
#include <thread>  
  
using namespace std::chrono_literals;  
  
int main()  
{  
    auto core = boost::log::core::get();  
    boost::log::sources::logger logger;  
    auto sink = boost::make_shared<boost::log::sinks::asynchronous_sink<boost::log::sinks::text_file_backend>>(boost::log::keywords::start_thread = false, boost::log::keywords::file_name = "c:\\temp\\file.log");  
    core->add_sink(sink);  
      
    // Queue up some stuff before starting the feeding thread  
    BOOST_LOG(logger) << "Hello, world!";  
    BOOST_LOG(logger) << "Hello, world!";  
    BOOST_LOG(logger) << "Hello, world!";  
    BOOST_LOG(logger) << "Hello, world!";  
    BOOST_LOG(logger) << "Hello, world!";  
    BOOST_LOG(logger) << "Hello, world!";  
      
    std::thread feeder([sink]() {  
        std::cout << "foo"; // <- create a slight delay  
        sink->run();  
    });  
      
    core->flush();  
      
    // *BOOM* Unhandled C++ exception from the feeding thread for the sink  
      
    sink->stop();  
    feeder.join();  
}  
  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2020-09-23 17:37:34 UTC  
> Url: https://github.com/boostorg/log/issues/131#issuecomment-697762270  

Thanks for the report. At the moment you could work around it by running the internal feeding thread in the sink frontend (by not setting `start_thread` parameter to `false`).

---

## Comment 2

> Username: boldbyteboss  
> Created at: 2020-09-23 17:46:47 UTC  
> Url: https://github.com/boostorg/log/issues/131#issuecomment-697777549  

Thanks. Does allowing the constructor to start the thread eliminate the race (perhaps by recording the feeding thread ID prior to returning) or does it merely shrink the window in which the race might occur? I'm assuming the constructor does not wait to return until the thread is actually running...

---

## Comment 3

> Username: Lastique  
> Created at: 2020-09-23 18:15:31 UTC  
> Updated at: 2020-09-23 18:20:47 UTC  
> Url: https://github.com/boostorg/log/issues/131#issuecomment-697824010  

The constructor doesn't wait, but instead `flush` verifies that the internal thread is joinable (which it is if the thread was spawned). I.e. the internal thread is joinable after the constructor returns, even if it hasn't called `run` yet, and this will prevent `flush` from processing log records and cause conflict.

---

## Comment 4

> Username: boldbyteboss  
> Created at: 2020-09-23 18:26:06 UTC  
> Url: https://github.com/boostorg/log/issues/131#issuecomment-697841146  

Ah, I see. Just read the code for start_feeding_thread and flush and I see the difference between the implicit start and providing my own feeding thread. Will see if we can leverage this in the mean time.
