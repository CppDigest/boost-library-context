# #144 - multi thread performance of severity_channel_logger_mt [Closed]

> Username: redboltz  
> Created at: 2021-03-10 10:52:11 UTC  
> Updated at: 2021-03-11 09:47:56 UTC  
> Closed at: 2021-03-10 14:40:48 UTC  
> Url: https://github.com/boostorg/log/issues/144  

## Issue overview  
I'm using boost.log. I used `boost::log::sources::severity_logger_mt`. Recently I replaced it with `boost::log::sources::severity_channel_logger_mt`. Then my program running speed became 1.4 times slower even if log is filtered (not output).  
On multi threaded environment, `boost::log::sources::severity_channel_logger_mt` is 7.9 times slower than `boost::log::sources::severity_logger_mt`. It is more significant performance degrade than single threaded environment.   
  
I start thinking that I revert to `boost::log::sources::severity_logger_mt`.  
  
But I'm not sure why adding `channel` functionality make a big performance disadvantage. So I report the issue.  
Any ideas ?  
  
## Minimal code that reproduce the issue  
  
```cpp  
// Compile  
// clang++ channel_mt_perf.cpp -DBOOST_LOG_DYN_LINK -lboost_log -lpthread -O3  
  
#include <iostream>  
#include <chrono>  
#include <thread>  
  
#include <boost/log/core.hpp>  
#include <boost/log/attributes.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/expressions/keyword.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/sources/global_logger_storage.hpp>  
#include <boost/log/sources/severity_channel_logger.hpp>  
#include <boost/log/sources/severity_logger.hpp>  
#include <boost/log/trivial.hpp>  
  
#include <boost/lexical_cast.hpp>  
  
namespace log = boost::log;  
  
enum class severity_level {  
    trace,  
    debug,  
    info,  
    warning,  
    error,  
    fatal  
};  
BOOST_LOG_ATTRIBUTE_KEYWORD(severity, "Severity", severity_level)  
  
using global_logger_chsv_t = log::sources::severity_channel_logger_mt<severity_level, std::string>;  
using global_logger_sv_t = log::sources::severity_logger_mt<severity_level>;  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(logger_chsv, global_logger_chsv_t);  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(logger_sv, global_logger_sv_t);  
  
void test_chsv(std::size_t threads, std::size_t times) {  
    std::vector<std::thread> ts;  
    ts.reserve(threads);  
    for (std::size_t t = 0; t != threads; ++t) {  
        ts.emplace_back(  
            [&] {  
                for (std::size_t i = 0; i != times; ++i) {  
                    BOOST_LOG_STREAM_CHANNEL_SEV(logger_chsv::get(), "test", severity_level::trace) << "hello";  
                }  
            }  
        );  
    }  
    for (auto& t : ts) t.join();  
}  
  
  
void test_sv(std::size_t threads, std::size_t times) {  
    std::vector<std::thread> ts;  
    ts.reserve(threads);  
    for (std::size_t t = 0; t != threads; ++t) {  
        ts.emplace_back(  
            [&] {  
                for (std::size_t i = 0; i != times; ++i) {  
                    BOOST_LOG_STREAM_SEV(logger_sv::get(), severity_level::trace) << "hello";  
                }  
            }  
        );  
    }  
    for (auto& t : ts) t.join();  
}  
  
int main(int argc, char* argv[]) {  
    if (argc != 3) {  
        std::cout << "Usage: " << argv[0] << " threads times" << std::endl;  
        return -1;  
    }  
    auto threads = boost::lexical_cast<std::size_t>(argv[1]);  
    auto times = boost::lexical_cast<std::size_t>(argv[2]);  
  
    boost::log::add_common_attributes();  
    boost::log::core::get()->set_filter(  
        severity >= severity_level::fatal  
    );  
  
    {  
        auto start = std::chrono::system_clock::now();  
        test_chsv(threads, times);  
        auto end = std::chrono::system_clock::now();  
        std::cout  
            << "ch sv"  
            << " threads: " << threads  
            << " times: " << times  
            << " "  
            << std::chrono::duration_cast<std::chrono::milliseconds>(end-start).count() << " ms" << std::endl;  
    }  
    {  
        auto start = std::chrono::system_clock::now();  
        test_sv(threads, times);  
        auto end = std::chrono::system_clock::now();  
        std::cout  
            << "sv   "  
            << " threads: " << threads  
            << " times: " << times  
            << " "  
            << std::chrono::duration_cast<std::chrono::milliseconds>(end-start).count() << " ms" << std::endl;  
    }  
}  
```  
  
## Metrics  
  
### 1 thread, 10,000,000 times:  
  
```  
./a.out 1 10000000  
ch sv threads: 1 times: 10000000 1329 ms  
sv    threads: 1 times: 10000000 930 ms  
```  
  
`severity_channel_logger_mt` is 1.4 times slower than `severity_logger_mt`.  
  
### 10 threads, 1,000,000 times:  
  
```  
./a.out 10 1000000  
ch sv threads: 10 times: 1000000 6859 ms  
sv    threads: 10 times: 1000000 1151 ms  
```  
  
`severity_channel_logger_mt` is 7.9 times slower than `severity_logger_mt`.  
  
  
## Environment  
- boost verion  
   - 1.75.0  
- OS  
   - `Linux archboltz 5.10.16-arch1-1 #1 SMP PREEMPT Sat, 13 Feb 2021 20:50:18 +0000 x86_64 GNU/Linux`

---

## Comment 1

> Username: Lastique  
> Created at: 2021-03-10 14:40:48 UTC  
> Url: https://github.com/boostorg/log/issues/144#issuecomment-795518005  

Your test uses `BOOST_LOG_STREAM_CHANNEL_SEV` macro, which sets the channel name for every log record. For thread safety, this also involves locking a mutex to protect the channel name attribute. You can avoid some of this overhead if you only initialize the channel name on logger construction and then use macros without specifying the channel name.  
  
There will still be some overhead because the channel feature requires to lock the logger mutex on `open_record` call even if the channel name is not modified, which the severity level feature doesn't need. This is because the `open_record` needs to ensure that the returned log record contains the channel name that was set by the `open_record` call (or was set before it if the channel is not specified in the call). The severity level feature works around this as it uses TLS internally to store the severity level.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-03-10 14:50:00 UTC  
> Updated at: 2021-03-10 14:51:06 UTC  
> Url: https://github.com/boostorg/log/issues/144#issuecomment-795537849  

If you care about multithreaded performance, the logger may become the point of thread contention. In this case it is recommended to use different non-`_mt` loggers in different threads or reduce the contention by changing your application design so that threads are less likely to share a logger. In particular, you should definitely avoid using global loggers.

---

## Comment 3

> Username: redboltz  
> Created at: 2021-03-11 02:03:20 UTC  
> Url: https://github.com/boostorg/log/issues/144#issuecomment-796371212  

Thank you for the valuable comment.  
  
I did some more tests.  
  
First, I replaced   
```cpp  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(logger_chsv, global_logger_chsv_t);  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(logger_sv, global_logger_sv_t);  
```  
  
with  
  
```cpp  
thread_local global_logger_chsv_t logger_chsv;  
thread_local global_logger_sv_t logger_sv;  
```  
  
and removed `::get()` to access singleton.  
  
(Used thread_local storage instead of global one)  
  
Then I got the following results.  
  
### single thread  
```  
./a.out 1 10000000  
ch sv threads: 1 times: 10000000 2098 ms  
sv    threads: 1 times: 10000000 1732 ms  
```  
  
### multi threads  
  
```  
./a.out 10 1000000  
ch sv threads: 10 times: 1000000 1058 ms  
sv    threads: 10 times: 1000000 1059 ms  
```  
  
The performance on multi threads is dramatically improved.  
  
If `thread_local` would be a solution, that satisfies my use case perfectly.  
  
At first I tried that logger as a member variable of my class.  
  
```cpp  
class my {  
public:  
    void do_some() {  
        BOOST_LOG_STREAM_CHANNEL_SEV(logger_chsv_, "test", severity_level::trace) << "hello";  
    }  
private:  
    global_logger_chsv_t logger_chsv_:  
};  
```  
  
However, it couldn't be the solution because `do_some()` may be called from the different threads.  
  
The next idea was using function local variable. I'm not sure the cost of constructing logger object.  
But I guess that each time constructing logger is inefficient.  
  
```cpp  
class my {  
public:  
    void do_some() {  
        global_logger_chsv_t logger_chsv:  
        BOOST_LOG_STREAM_CHANNEL_SEV(logger_chsv, "test", severity_level::trace) << "hello";  
    }  
private:  
};  
```  
  
So I choose `thread_local` variable.  
  
In addition, after `thread_local` replacement is applied,  I also replaced  
  
```cpp  
using global_logger_chsv_t = log::sources::severity_channel_logger_mt<severity_level, std::string>;  
using global_logger_sv_t = log::sources::severity_logger_mt<severity_level>;  
```  
  
with  
  
```cpp  
using global_logger_chsv_t = log::sources::severity_channel_logger<severity_level, std::string>;  
using global_logger_sv_t = log::sources::severity_logger<severity_level>;  
```  
  
(Removed `_mt')  
  
Then I got the following results.  
  
### single thread  
```  
./a.out 1 10000000  
ch sv threads: 1 times: 10000000 2014 ms  
sv    threads: 1 times: 10000000 1521 ms  
```  
  
### multi threads  
  
```  
./a.out 10 1000000  
ch sv threads: 10 times: 1000000 1013 ms  
sv    threads: 10 times: 1000000 1009 ms  
```  
  
## Questions  
  
### Q1 Using non `_mt` logger with `thread_local` storage safe on multi threads environment ?  
I read again https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/design.html  
  
I think that my loggers are kinds of custom log sources. Thanks to `thread_local` storage, I think that the loggers themselves are thread safe. Is that right?  
  
However, I'm not sure about Logging core. The diagram logging core is crosscutting. I'd like to know about thread safety of my usage.  
  
### Q2 Aren't log outputs interleaved while I use `locked_backend` as Sink backend?  
I added longer output string to my example code. The each line of the outputs are not interleaved. It is expected behavior.  
Is it achieved by `locked_backend` ?  
  
## Here is whole updated code.  
  
```cpp  
// Compile  
// clang++ channel_mt_perf.cpp -DBOOST_LOG_DYN_LINK -lboost_log -lpthread -O3  
  
#include <iostream>  
#include <chrono>  
#include <thread>  
#include <algorithm>  
  
#include <boost/log/core.hpp>  
#include <boost/log/attributes.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/expressions/keyword.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/sources/global_logger_storage.hpp>  
#include <boost/log/sources/severity_channel_logger.hpp>  
#include <boost/log/sources/severity_logger.hpp>  
#include <boost/log/trivial.hpp>  
  
#include <boost/lexical_cast.hpp>  
  
namespace log = boost::log;  
  
enum class severity_level {  
    trace,  
    debug,  
    info,  
    warning,  
    error,  
    fatal  
};  
BOOST_LOG_ATTRIBUTE_KEYWORD(severity, "Severity", severity_level)  
  
using global_logger_chsv_t = log::sources::severity_channel_logger<severity_level, std::string>;  
using global_logger_sv_t = log::sources::severity_logger<severity_level>;  
#if 0  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(logger_chsv, global_logger_chsv_t);  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(logger_sv, global_logger_sv_t);  
#endif  
  
thread_local global_logger_chsv_t logger_chsv;  
thread_local global_logger_sv_t logger_sv;  
  
void test_chsv(std::size_t threads, std::size_t times) {  
    std::vector<std::thread> ts;  
    ts.reserve(threads);  
    for (std::size_t t = 0; t != threads; ++t) {  
        ts.emplace_back(  
            [t, times] {  
                for (std::size_t i = 0; i != times; ++i) {  
                    std::string s;  
                    std::fill_n(std::back_inserter(s), 40, 'a' + t);  
                    BOOST_LOG_STREAM_CHANNEL_SEV(logger_chsv, "test", severity_level::trace) << __LINE__ << " " << s;  
                }  
            }  
        );  
    }  
    for (auto& t : ts) t.join();  
}  
  
  
void test_sv(std::size_t threads, std::size_t times) {  
    std::vector<std::thread> ts;  
    ts.reserve(threads);  
    for (std::size_t t = 0; t != threads; ++t) {  
        ts.emplace_back(  
            [t, times] {  
                for (std::size_t i = 0; i != times; ++i) {  
                    std::string s;  
                    std::fill_n(std::back_inserter(s), 40, 'A' + t);  
                    BOOST_LOG_STREAM_SEV(logger_sv, severity_level::trace) << __LINE__ << " " << s;  
                }  
            }  
        );  
    }  
    for (auto& t : ts) t.join();  
}  
  
int main(int argc, char* argv[]) {  
    if (argc != 3) {  
        std::cout << "Usage: " << argv[0] << " threads times" << std::endl;  
        return -1;  
    }  
    auto threads = boost::lexical_cast<std::size_t>(argv[1]);  
    auto times = boost::lexical_cast<std::size_t>(argv[2]);  
  
    boost::log::add_common_attributes();  
    boost::log::core::get()->set_filter(  
        severity >= severity_level::warning  
    );  
  
    {  
        auto start = std::chrono::system_clock::now();  
        test_chsv(threads, times);  
        auto end = std::chrono::system_clock::now();  
        std::cout  
            << "ch sv"  
            << " threads: " << threads  
            << " times: " << times  
            << " "  
            << std::chrono::duration_cast<std::chrono::milliseconds>(end-start).count() << " ms" << std::endl;  
    }  
    {  
        auto start = std::chrono::system_clock::now();  
        test_sv(threads, times);  
        auto end = std::chrono::system_clock::now();  
        std::cout  
            << "sv   "  
            << " threads: " << threads  
            << " times: " << times  
            << " "  
            << std::chrono::duration_cast<std::chrono::milliseconds>(end-start).count() << " ms" << std::endl;  
    }  
}  
```

---

## Comment 4

> Username: Lastique  
> Created at: 2021-03-11 09:10:33 UTC  
> Updated at: 2021-03-11 09:13:01 UTC  
> Url: https://github.com/boostorg/log/issues/144#issuecomment-796586915  

> At first I tried that logger as a member variable of my class. However, it couldn't be the solution because `do_some()` may be called from the different threads.  
  
This may not be a problem, if the threads are not likely to contend for a shared resource. Usually, there is some data in the class that requires a mutex to protect from concurrent access. You could use that same mutex to protect a non-`_mt` logger as well.  
  
Also, in your example you are still using `BOOST_LOG_STREAM_CHANNEL_SEV`.  
  
> The next idea was using function local variable. I'm not sure the cost of constructing logger object.  
But I guess that each time constructing logger is inefficient.  
  
Yes, constructing a logger may be expensive.  
  
> Using non `_mt` logger with `thread_local` storage safe on multi threads environment ?  
  
`_mt` loggers contain a mutex internally to protect from concurrent access. So long as you don't pass references of `thread_local` loggers between threads, you should not need the mutex, as only a single thread will be using a given logger.  
  
> I think that my loggers are kinds of custom log sources.  
  
In that diagram, "custom log sources" means sources that are not provided by the library (i.e. written by the user). You're using loggers, so that doesn't apply to you. It doesn't really matter how you store the loggers, from the design perspective, the loggers still belong to the left part of the diagram.  
  
> However, I'm not sure about Logging core. The diagram logging core is crosscutting. I'd like to know about thread safety of my usage.  
  
All loggers use the one logging core. It is thread-safe regardless of the kind of loggers you use.  
  
> Q2 Aren't log outputs interleaved while I use `locked_backend` as Sink backend?  
  
I'm not sure what you mean by interleaving.  
  
`locked_backend` returns a pointer to the sink backend that keeps the frontend mutex locked while you operate on the backend. This means that while the pointer exists, no other thread will be able to pass a log record to the sink or call `locked_backend` (in the latter case the thread would block). So whatever changes on the backend you perform, they will happen between log records are processed by the backend.  
  
As for interleaving, if you mean whether the contents of two log records can get output interleaved due to thread contention then the answer is no. Each log record will be output as a whole, this is guaranteed by the sink frontend that performs thread synchronization. However, the order in which log records from *different* threads are written is [not defined](https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/rationale/why_weak_record_ordering.html).

---

## Comment 5

> Username: redboltz  
> Created at: 2021-03-11 09:24:06 UTC  
> Url: https://github.com/boostorg/log/issues/144#issuecomment-796595378  

Thank you very much!!  
  
For my usecase, non _mt loggers as `thread_local` storage seems to be the best solution.  
  
> > Q2 Aren't log outputs interleaved while I use `locked_backend` as Sink backend?  
>   
> I'm not sure what you mean by interleaving.  
  
I mean mixed output by two threads.  
  
Let's say thread1 outputs "AAAAA", and thread2 outputs "BBBBB".  
If I use `std::cout` instead of Boost.Log, The output may "AABABABAABB".  
I use the word **interleave** as this situation.   
  
When I use non _mt logger with thread_local storage, it doesn't happen. So I wanted to know which part of Boost.Log avoid the mix outputs.  
  
> `locked_backend` returns a pointer to the sink backend that keeps the frontend mutex locked while you operate on the backend. This means that while the pointer exists, no other thread will be able to pass a log record to the sink or call `locked_backend` (in the latter case the thread would block). So whatever changes on the backend you perform, they will happen between log records are processed by the backend.  
>   
> As for interleaving, if you mean whether two log records can get output merged together due to thread contention then the answer is no. Each log record will be output as a whole, this is guaranteed by the sink frontend that performs thread synchronization. However, the order in which log records from _different_ threads are written is [not defined](https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/rationale/why_weak_record_ordering.html).  
  
I understand. Let me confirm. In my example situation, the output is "AAAAABBBBB" or "BBBBBAAAAA". A and B never mixed.  
It is expected behavior. And it isn't related to locked_backend. Frontend avoids the mix outputs.  
  
Thank you for the answer and thank you for writing the great library!

---

## Comment 6

> Username: Lastique  
> Created at: 2021-03-11 09:47:56 UTC  
> Url: https://github.com/boostorg/log/issues/144#issuecomment-796609804  

> Let me confirm. In my example situation, the output is "AAAAABBBBB" or "BBBBBAAAAA". A and B never mixed.  
It is expected behavior.  
  
Correct.  
  
> And it isn't related to `locked_backend`. Frontend avoids the mix outputs.  
  
It is related in the sense that `locked_backend` locks the same mutex in the frontend that is locked when a log record is processed by the sink. So the frontend protects the backend from concurrent access - whether the access is via `locked_backend` or by passing log records, it doesn't matter.
