# #212 - How to add timezone info for log entries and log filename [Closed]

> Username: lijh8  
> Created at: 2023-07-01 23:59:03 UTC  
> Updated at: 2023-07-02 13:14:08 UTC  
> Closed at: 2023-07-02 09:17:30 UTC  
> Url: https://github.com/boostorg/log/issues/212  

Hi, how can i add timezone info for log entries:  
  
   ` % expr::attr< boost::posix_time::ptime >("TimeStamp") `  
  
and for log filename:  
  
    ` auto pattern = "_%Y%m%d_%H%M%S_%5N.log"; `  
  
Thanks  
  
log entries:  
```  
2023-Jul-02 07:40:38.952122: main.cpp:39: trace msg!  
2023-Jul-02 07:40:38.952363: main.cpp:40: debug msg!  
2023-Jul-02 07:40:38.952670: main.cpp:41: info msg!  
2023-Jul-02 07:40:38.953011: main.cpp:42: warning msg!  
2023-Jul-02 07:40:38.953460: main.cpp:43: error msg!  
2023-Jul-02 07:40:38.954591: main.cpp:44: fatal msg!  
```  
---  
filename pattern:  
```  
 foo_20230702_074038_00000.log  
 foo_20230702_074320_00001.log  
 foo_20230702_074320_00002.log  
 foo_20230702_074320_00003.log  
 foo_20230702_074320_00004.log  
```  
---  
```  
  
// log.h:  
  
#ifndef log_h  
#define log_h  
  
#include <string>  
#include <boost/log/trivial.hpp>  
  
// Andrey Semashev, 2016  
// https://stackoverflow.com/questions/35895199/boost-log-and-severity-local-attributes/35919119#35919119  
// #define LOG(lg, sev) \  
//     BOOST_LOG_SEV(lg, sev) << "[" << __FILE__ << ":" << __LINE__ << "]: "  
  
#define LOG(severity) \  
    BOOST_LOG_TRIVIAL(severity) << __FILE__ << ":" << __LINE__ << ": "  
  
void boost_log_init(const std::string log_dir, const std::string name);  
  
#endif  
  
```  
---  
```  
  
// log.cpp  
  
// ../boost_1_82_0/libs/log/example/rotating_file/main.cpp  
  
// $ export LD_LIBRARY_PATH=/home/ljh/Downloads/boost_1_82_0/stage/lib  
  
// #define BOOST_LOG_DYN_LINK 1  
  
#include <stdexcept>  
#include <string>  
#include <iostream>  
#include <memory>  
#include <boost/smart_ptr/shared_ptr.hpp>  
#include <boost/shared_ptr.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
  
#include <boost/log/common.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/attributes.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/sinks/sync_frontend.hpp>  
#include <boost/log/sinks/text_file_backend.hpp>  
#include <boost/log/sinks/text_ostream_backend.hpp>  
#include <boost/core/null_deleter.hpp>  
  
#include "log.h"  
  
namespace logging = boost::log;  
namespace attrs = boost::log::attributes;  
// namespace src = boost::log::sources;  
namespace sinks = boost::log::sinks;  
namespace expr = boost::log::expressions;  
namespace keywords = boost::log::keywords;  
  
using boost::shared_ptr;  
  
// enum { LOG_RECORDS_TO_WRITE = 10000 };  
  
void boost_log_init(const std::string log_dir, const std::string name) {  
  
    auto format = expr::format("%1%: %2%")  
            % expr::attr< boost::posix_time::ptime >("TimeStamp")  
            % expr::smessage;  
  
    // Create a text file sink  
    typedef sinks::synchronous_sink< sinks::text_file_backend > file_sink;  
    auto pattern = "_%Y%m%d_%H%M%S_%5N.log";  
    shared_ptr< file_sink > sink(new file_sink(  
        keywords::file_name = log_dir + "/" + name + pattern,   // file name pattern  
        keywords::target_file_name = name + pattern,            // file name pattern  
        keywords::rotation_size = 30 * 1024                     // rotation size, in characters  
        ));  
  
    // Set up where the rotated files will be stored  
    sink->locked_backend()->set_file_collector(sinks::file::make_collector(  
        keywords::target = log_dir,                             // where to store rotated files  
        keywords::max_size = 16 * 1024 * 1024,                  // maximum total size of the stored files, in bytes  
        keywords::min_free_space = 100 * 1024 * 1024,           // minimum free space on the drive, in bytes  
        keywords::max_files = 7                                 // maximum number of stored files  
        ));  
  
    // Upon restart, scan the target directory for files matching the file_name pattern  
    sink->locked_backend()->scan_for_files();  
    sink->set_formatter(format);  
  
    // Add it to the core  
    logging::core::get()->add_sink(sink);  
  
    // Add a console sink  
    typedef sinks::synchronous_sink< sinks::text_ostream_backend > console_sink;  
    shared_ptr< console_sink > consoleSink(new console_sink());  
    consoleSink->locked_backend()->add_stream(boost::shared_ptr<std::ostream>(&std::clog, boost::null_deleter()));  
    consoleSink->set_formatter(format);  
  
    consoleSink->set_filter(logging::trivial::severity >= logging::trivial::trace);  
    logging::core::get()->add_sink(consoleSink);  
  
    // Add some attributes too  
    logging::core::get()->add_global_attribute("TimeStamp", attrs::local_clock());  
  
}  
  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2023-07-02 09:17:30 UTC  
> Url: https://github.com/boostorg/log/issues/212#issuecomment-1616509085  

You can't, because Boost.DateTime currently does not provide a way to obtain the current timezone from the system. I'm actually not sure there is a more or less standard way to do this.  
  
If you have a timezone through some external means, you could create an attribute that generates [`boost::local_time::local_date_time`](https://www.boost.org/doc/libs/1_82_0/doc/html/date_time/local_time.html#date_time.local_time.local_date_time) with the time zone, and then use its [locale facet](https://www.boost.org/doc/libs/1_82_0/doc/html/date_time/date_time_io.html) to format the date and time with the timezone. You can use the [`basic_clock`](https://github.com/boostorg/log/blob/b3ffed4ec2cdb376e9355a17d5e89ec2f3bc893a/include/boost/log/attributes/clock.hpp) attribute with your own traits (see [here](https://github.com/boostorg/log/blob/b3ffed4ec2cdb376e9355a17d5e89ec2f3bc893a/include/boost/log/attributes/time_traits.hpp) for examples of clock traits).  
  
There is no workaround for file names. This was already reported in https://github.com/boostorg/log/issues/210.

---

## Comment 2

> Username: lijh8  
> Created at: 2023-07-02 13:03:01 UTC  
> Url: https://github.com/boostorg/log/issues/212#issuecomment-1616653811  

```  
#define LOG(severity) \  
    now = time(NULL); \  
    strftime(tz, sizeof(tz), "%z", localtime(&now)); \  
    BOOST_LOG_TRIVIAL(severity) << " " << tz << " " << __FILE__ << ":" << __LINE__ << ": "  
```  
  
I tried adding the timezone in the LOG macro which was written by you in 2016. But it outputs the timezone after severity `+0800`. It also calls extra functions time() and strftime(). I don't think you'll like it.  
  
```  
2023-Jul-02 20:52:01.079777 [info   ]  +0800 main.cpp:45: info msg!  
2023-Jul-02 20:52:01.080239 [warning]  +0800 main.cpp:46: warning msg!  
2023-Jul-02 20:52:01.098602 [error  ]  +0800 main.cpp:47: error msg!  
```

---

## Comment 3

> Username: Lastique  
> Created at: 2023-07-02 13:14:08 UTC  
> Url: https://github.com/boostorg/log/issues/212#issuecomment-1616657693  

I didn't mean to modify the logging macro, I meant to add your own attribute for timestamps instead of `attrs::local_clock`, which would contain time zone information, and then format it.
