# #153 - boost log locks on exit [Closed]

> Username: ycyclop  
> Created at: 2021-06-23 07:37:18 UTC  
> Updated at: 2021-07-13 16:13:10 UTC  
> Closed at: 2021-06-28 12:36:07 UTC  
> Url: https://github.com/boostorg/log/issues/153  

I have the following (stripped down) logger code that works as expected except when the application exists in a specific scenario. When the logger seems to be writing the log file and the application exists, the logger locks and the application does not exit. Here is the logger code sample and the main that works with it that can reproduce the problem:  
```  
#include <boost/log/sinks/async_frontend.hpp>  
#include <boost/log/sinks/drop_on_overflow.hpp>  
#include <boost/log/sinks/bounded_fifo_queue.hpp>  
#include <boost/log/sinks/text_ostream_backend.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/sources/severity_logger.hpp>  
#include <boost/log/trivial.hpp>  
#include <boost/log/utility/setup/file.hpp>  
#include <boost/log/attributes/clock.hpp>  
#include <boost/log/attributes/current_process_name.hpp>  
#include <boost/log/attributes/current_process_id.hpp>  
#include <boost/log/attributes/current_thread_id.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/support/date_time.hpp>  
#include <boost/log/utility/setup/console.hpp>  
  
namespace blog = boost::log;  
  
constexpr uint32_t LOG_QUEUE_BOUND_LINES = 10000;  
constexpr uint32_t MAX_FILE_SIZE_FOR_ROTATION_MB = (10 * 1024 * 1024); // 10MB  
constexpr uint32_t MAX_TIME_FOR_ROTATION_IN_HOURS = (24 * 7); // 1 week  
  
using LogAsyncSink = boost::log::sinks::asynchronous_sink<boost::log::sinks::text_file_backend,  
    boost::log::sinks::bounded_fifo_queue<  
    LOG_QUEUE_BOUND_LINES_NUM,  
    boost::log::sinks::drop_on_overflow>>;  
  
boost::log::sources::severity_logger_mt<boost::log::trivial::severity_level> _logger;  
  
boost::shared_ptr<LogAsyncSink> fileGlobalSink;  
  
#define LOG_FORMAT blog::expressions::stream \  
    << blog::expressions::format_date_time<boost::posix_time::ptime>("TimeStamp", "%Y-%m-%d %H:%M:%S.%f") \  
    << " [" \  
    << blog::expressions::attr<blog::trivial::severity_level>("Severity") \  
    << "] " \  
    << blog::expressions::attr<std::string>("Process") \  
    << ":" \  
    << blog::expressions::attr<blog::process_id>("ProcessID") \  
    << ":" \  
    << blog::expressions::attr<blog::thread_id>("ThreadID") \  
    << " " \  
    << blog::expressions::message  
  
boost::shared_ptr<LogAsyncSink> AddFile(const std::string& fileName)  
{  
    // No need to guard since this can be called only from functions that are guarded  
    boost::shared_ptr<LogAsyncSink> returnSink(nullptr);  
    boost::shared_ptr<blog::sinks::text_file_backend> textFile =  
        boost::make_shared<blog::sinks::text_file_backend>(blog::keywords::file_name = fileName);  
  
    textFile->set_file_name_pattern(fileName);  
  
    // set rolling mechanism  
    textFile->set_rotation_size(MAX_FILE_SIZE_FOR_ROTATION_MB);  
    textFile->set_time_based_rotation(  
        blog::sinks::file::rotation_at_time_interval(boost::posix_time::hours(MAX_TIME_FOR_ROTATION_IN_HOURS)));  
  
    boost::shared_ptr<LogAsyncSink> fileSink(new LogAsyncSink(textFile));  
    fileSink->set_formatter(  
        LOG_FORMAT  
    );  
    fileSink->locked_backend()->auto_flush(true);  
  
    returnSink = fileSink;  
    return returnSink;  
}  
  
void LoggerInit()  
{  
    std::string fileName = "logfilename.txt";  
    boost::shared_ptr<LogAsyncSink> fileAdded;  
  
    fileAdded = AddFile(fileName);  
    auto core = blog::core::get();  
    if (core != nullptr)  
    {  
        core->add_global_attribute("TimeStamp", blog::attributes::local_clock());  
        core->add_global_attribute("Process", blog::attributes::current_process_name());  
        core->add_global_attribute("ProcessID", blog::attributes::current_process_id());  
        core->add_global_attribute("ThreadID", blog::attributes::current_thread_id());  
  
        core->add_sink(fileAdded);  
        fileGlobalSink = fileAdded;  
    }  
}  
  
void StopLogging()  
{  
    boost::shared_ptr<blog::core> core = blog::core::get();  
  
    if (fileGlobalSink != nullptr)  
    {  
        if (core != nullptr)  
        {  
            // Remove the sink from the core, so that no records are passed to it  
            core->remove_sink(fileGlobalSink);  
        }  
  
        // Flush all log records that may have left buffered  
        fileGlobalSink->flush();  
  
        // Break the feeding loop  
        fileGlobalSink->stop();  
  
        fileGlobalSink.reset();  
    }  
}  
  
/******************************* main ****************************************/  
int main(int argc, char* argv[])  
{  
    LoggerInit();  
    BOOST_LOG_SEV(_logger, boost::log::trivial::trace) << "Test";  
//    Sleep(10); -> Min exists correctly no lock  
//    StopLogging(); -> everything closes correctly  
}  
```  
As you can see if I add the sleep, the logger finish logging and exists correctly, if I add the call to StopLogging() then the logger close everything correctly and the application end correctly. With out those two function the boost locks at: async_frontend.cpp in the stop() function:  
```  
while (m_StopRequested.load(boost::memory_order_acquire))  
                    m_BlockCond.wait(lock);  
```

---

## Comment 1

> Username: theroyn  
> Created at: 2021-06-23 09:36:52 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-866687197  

Some investigation showed the reason for the deadlock. While the main thread is stuck in the `asynchronous_sink::stop()` which was reached from the destructor of the log's `lazy_singleton`'s static instance, another thread is stuck on `asynchronous_sink::run()`, which internally reaches all the way to `boost::filesystem::detail::dot_path()`.  
`dot_path()` is called for the first time and it tries to initialize a function-local static variable called `fs::path dot_pth`.  
So we have construction of a function-local static variable during the destruction of a static variable, which is quite UB.  
On MSVC it seems the variable's construction is never completed and the execution never leaves `fs::path dot_pth`.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-06-23 11:27:47 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-866756623  

> So we have construction of a function-local static variable during the destruction of a static variable, which is quite UB.  
  
I don't see where the standard designates this as UB. If the compiler deadlocks in this case, it seems like a compiler issue.  
  
(I didn't reproduce this bug, so I can't confirm that this is what happens.)

---

## Comment 3

> Username: theroyn  
> Created at: 2021-06-23 11:35:39 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-866761166  

https://timsong-cpp.github.io/cppwp/basic.start.term#3  
Here clearly says ```If the completion of the constructor or dynamic initialization of an object with static storage duration strongly happens before that of another, the completion of the destructor of the second is sequenced before the initiation of the destructor of the first```.  
So this case forces a situation that clearly contradicts the standard, since "the completion of the destructor of the second" cannot possibly happen before the "initiation of the destructor of the first".

---

## Comment 4

> Username: Lastique  
> Created at: 2021-06-23 11:52:33 UTC  
> Updated at: 2021-06-23 11:54:29 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-866770524  

The keyword here is the leading "if". What this paragraph says is that the global destructors run in the reverse order of constructors (more precisely - the reverse order of constructors' completion). It does not require that no global constructors are run during destructors or the program termination stage in general.

---

## Comment 5

> Username: Lastique  
> Created at: 2021-06-23 12:55:29 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-866810574  

@ycyclop What Boost version are you using? What compiler and platform?

---

## Comment 6

> Username: ycyclop  
> Created at: 2021-06-23 13:40:15 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-866845604  

I tried with boost 1.72 & 1.76 same result on both versions  
VS 2019

---

## Comment 7

> Username: ycyclop  
> Created at: 2021-06-23 14:01:33 UTC  
> Updated at: 2021-06-24 05:15:58 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-866862738  

The bug does not reproduce on Linux ubuntu 18.04.1 with CMake 3.18.4 gcc 7.5.0

---

## Comment 8

> Username: Lastique  
> Created at: 2021-06-28 11:50:50 UTC  
> Updated at: 2021-06-28 11:54:40 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-869617971  

I had to fix the `LOG_QUEUE_BOUND_LINES_NUM` naming for compilation to succeed.  
  
This does not reproduce on my system. I'm running the latest VS2019 Community Edition, cl.exe version 19.29.30038.1. I'm building 64-bit binaries, both release and debug mode with Boost.Build.  
  
If you're seeing what @theroyn has described then this issue may be timing dependent. Can you provide backtraces of all threads while the test case is hanging to confirm?  
  
If this is indeed what's happening, I suggest you report the bug to Microsoft. Dynamic initialization and destruction of different objects should not deadlock. In the meantime, you've already found a workaround, which is to explicitly shutdown logging before returning from `main`. This is a good idea regardless of the deadlock issue.

---

## Comment 9

> Username: ycyclop  
> Created at: 2021-06-28 12:23:27 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-869639415  

Main thread:  
 	ntdll.dll!00007ffd9b75d9f4()	Unknown  
 	KernelBase.dll!00007ffd993c2ad7()	Unknown  
 	BoostLogTest.exe!boost::this_thread::interruptible_wait(void * handle_to_wait_for, const boost::detail::mono_platform_timepoint & timeout) Line 678	C++  
 	BoostLogTest.exe!boost::thread::join_noexcept() Line 447	C++  
>	BoostLogTest.exe!boost::thread::join() Line 765	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>::stop() Line 441	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>::~asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>() Line 325	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>::`scalar deleting destructor'(unsigned int)	C++  
 	BoostLogTest.exe!boost::checked_delete<boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>>(boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>> * x) Line 36	C++  
 	BoostLogTest.exe!boost::detail::sp_counted_impl_p<boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>>::dispose() Line 90	C++  
 	BoostLogTest.exe!boost::detail::sp_counted_base::release() Line 120	C++  
 	BoostLogTest.exe!boost::detail::shared_count::~shared_count() Line 436	C++  
 	BoostLogTest.exe!boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>::~shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>()	C++  
 	BoostLogTest.exe!boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>::`scalar deleting destructor'(unsigned int)	C++  
 	BoostLogTest.exe!std::_Default_allocator_traits<std::allocator<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>>>::destroy<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>>(std::allocator<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>> & __formal, boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink> * const _Ptr) Line 702	C++  
 	BoostLogTest.exe!std::_Destroy_range<std::allocator<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>>>(boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink> * _First, boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink> * const _Last, std::allocator<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>> & _Al) Line 962	C++  
 	BoostLogTest.exe!std::vector<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>,std::allocator<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>>>::_Destroy(boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink> * _First, boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink> * _Last) Line 1620	C++  
 	BoostLogTest.exe!std::vector<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>,std::allocator<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>>>::_Tidy() Line 1703	C++  
 	BoostLogTest.exe!std::vector<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>,std::allocator<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>>>::~vector<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>,std::allocator<boost::shared_ptr<boost::log::v2s_mt_nt6::sinks::sink>>>() Line 682	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::core::implementation::~implementation()	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::core::implementation::`scalar deleting destructor'(unsigned int)	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::core::~core() Line 495	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::core::`scalar deleting destructor'(unsigned int)	C++  
 	BoostLogTest.exe!boost::checked_delete<boost::log::v2s_mt_nt6::core>(boost::log::v2s_mt_nt6::core * x) Line 36	C++  
 	BoostLogTest.exe!boost::detail::sp_counted_impl_p<boost::log::v2s_mt_nt6::core>::dispose() Line 90	C++  
 	BoostLogTest.exe!boost::detail::sp_counted_base::release() Line 120	C++  
 	BoostLogTest.exe!boost::detail::shared_count::~shared_count() Line 436	C++  
 	BoostLogTest.exe!boost::shared_ptr<boost::log::v2s_mt_nt6::core>::~shared_ptr<boost::log::v2s_mt_nt6::core>()	C++  
 	BoostLogTest.exe!`boost::log::v2s_mt_nt6::aux::lazy_singleton<boost::log::v2s_mt_nt6::core::implementation,boost::shared_ptr<boost::log::v2s_mt_nt6::core>>::get_instance'::`2'::`dynamic atexit destructor for 'instance''()	C++  
 	ucrtbased.dll!00007ffd42125107()	Unknown  
 	ucrtbased.dll!00007ffd42124b15()	Unknown  
 	ucrtbased.dll!00007ffd42124c4a()	Unknown  
 	ucrtbased.dll!00007ffd421252b1()	Unknown  
 	ucrtbased.dll!00007ffd42124481()	Unknown  
 	ucrtbased.dll!00007ffd4212432d()	Unknown  
 	ucrtbased.dll!00007ffd4212439a()	Unknown  
 	ucrtbased.dll!00007ffd42124614()	Unknown  
 	ucrtbased.dll!00007ffd421249a6()	Unknown  
 	BoostLogTest.exe!__scrt_common_main_seh() Line 297	C++  
 	BoostLogTest.exe!__scrt_common_main() Line 331	C++  
 	BoostLogTest.exe!mainCRTStartup(void * __formal) Line 17	C++  
 	kernel32.dll!00007ffd9b0a7c24()	Unknown  
 	ntdll.dll!00007ffd9b72d721()	Unknown  
  
worker thread 1  
>	ntdll.dll!00007ffd9b760834()	Unknown  
 	ntdll.dll!00007ffd9b6f3fe0()	Unknown  
 	kernel32.dll!00007ffd9b0a7c24()	Unknown  
 	ntdll.dll!00007ffd9b72d721()	Unknown  
  
worker thread 2  
>	ntdll.dll!00007ffd9b760834()	Unknown  
 	ntdll.dll!00007ffd9b6f3fe0()	Unknown  
 	kernel32.dll!00007ffd9b0a7c24()	Unknown  
 	ntdll.dll!00007ffd9b72d721()	Unknown  
  
worker thread 3  
 	ntdll.dll!00007ffd9b7607d4()	Unknown  
 	ntdll.dll!00007ffd9b6c7619()	Unknown  
 	ntdll.dll!00007ffd9b6c74d2()	Unknown  
 	ntdll.dll!00007ffd9b6c72fd()	Unknown  
 	ntdll.dll!00007ffd9b6db576()	Unknown  
 	ntdll.dll!00007ffd9b6db3c0()	Unknown  
 	ucrtbased.dll!00007ffd42106cf7()	Unknown  
 	ucrtbased.dll!00007ffd421072c5()	Unknown  
 	ucrtbased.dll!00007ffd42124b6a()	Unknown  
 	ucrtbased.dll!00007ffd42124bea()	Unknown  
 	ucrtbased.dll!00007ffd4212537b()	Unknown  
 	ucrtbased.dll!00007ffd4212526a()	Unknown  
>	BoostLogTest.exe!_onexit(int(*)() function) Line 261	C++  
 	BoostLogTest.exe!atexit(void(*)() function) Line 275	C++  
 	BoostLogTest.exe!boost::filesystem::detail::dot_path() Line 703	C++  
 	BoostLogTest.exe!boost::filesystem::path::extension() Line 360	C++  
 	BoostLogTest.exe!boost::filesystem::detail::make_permissions(const boost::filesystem::path & p, unsigned long attr) Line 40	C++  
 	BoostLogTest.exe!boost::filesystem::detail::status(const boost::filesystem::path & p, boost::system::error_code * ec) Line 2656	C++  
 	BoostLogTest.exe!boost::filesystem::detail::create_directories(const boost::filesystem::path & p, boost::system::error_code * ec) Line 1475	C++  
 	BoostLogTest.exe!boost::filesystem::create_directories(const boost::filesystem::path & p) Line 373	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::sinks::text_file_backend::consume(const boost::log::v2s_mt_nt6::record_view & rec, const std::string & formatted_message) Line 1386	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::sinks::basic_formatting_sink_frontend<char>::feed_record<boost::recursive_mutex,boost::log::v2s_mt_nt6::sinks::text_file_backend>(const boost::log::v2s_mt_nt6::record_view & rec, boost::recursive_mutex & backend_mutex, boost::log::v2s_mt_nt6::sinks::text_file_backend & backend) Line 465	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>::run() Line 398	C++  
 	BoostLogTest.exe!boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>::run_func::operator()() Line 166	C++  
 	BoostLogTest.exe!boost::detail::thread_data<boost::log::v2s_mt_nt6::sinks::asynchronous_sink<boost::log::v2s_mt_nt6::sinks::text_file_backend,boost::log::v2s_mt_nt6::sinks::bounded_fifo_queue<10000,boost::log::v2s_mt_nt6::sinks::drop_on_overflow>>::run_func>::run() Line 121	C++  
 	BoostLogTest.exe!boost::`anonymous namespace'::thread_start_function(void * param) Line 296	C++  
 	ucrtbased.dll!00007ffd4212542c()	Unknown  
 	kernel32.dll!00007ffd9b0a7c24()	Unknown  
 	ntdll.dll!00007ffd9b72d721()	Unknown

---

## Comment 10

> Username: Lastique  
> Created at: 2021-06-28 12:36:07 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-869647347  

Thanks. Yes, it looks like @theroyn was correct. I'm sorry, but I don't see what I can do about it in Boost.Log. Please, report this to Microsoft.

---

## Comment 11

> Username: Lastique  
> Created at: 2021-06-28 23:56:14 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-870123685  

@ycyclop I've reworked path globals in Boost.Filesystem, the updated code is now in the current develop branch and should be part of the upcoming Boost 1.77. This new implementation should work around this MSVC issue. Could you test if it works for you?

---

## Comment 12

> Username: ycyclop  
> Created at: 2021-06-29 11:48:21 UTC  
> Updated at: 2021-06-29 11:48:38 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-870525046  

for some reason I'm having problem building boost 1_76 from source files that I did not encounter with previous versions. I'll try and see later why I have this issue and try again. b2 does not build the library for some reason.

---

## Comment 13

> Username: ycyclop  
> Created at: 2021-07-13 13:01:27 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-879067143  

@Lastique The fix was checked and it works correctly. no lock when the application closes in the supplied scenario.

---

## Comment 14

> Username: Lastique  
> Created at: 2021-07-13 16:10:14 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-879217960  

Thanks for testing.

---

## Comment 15

> Username: Lastique  
> Created at: 2021-07-13 16:13:10 UTC  
> Url: https://github.com/boostorg/log/issues/153#issuecomment-879220141  

To be clear, the bug is still in the compiler/standard library, and it should still be reported to MS. The change in Boost.Filesystem was motivated by different issues (global initialization order issues) and worked around the deadlock problem only as a side effect.
