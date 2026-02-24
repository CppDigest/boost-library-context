# #235 - Retrieve timezone for log messages [Closed]

> Username: lijh8  
> Created at: 2024-09-24 14:40:56 UTC  
> Updated at: 2024-09-25 15:15:00 UTC  
> Closed at: 2024-09-24 16:00:05 UTC  
> Url: https://github.com/boostorg/log/issues/235  

Hi, i finally get it done by making an additional call to localtime to get the timezone.  
The format `%Z` does not yet work here:  
```   
  expr::format_date_time<boost::posix_time::ptime>(  
    "TimeStamp", "%Y-%m-%d %H:%M:%S.%f")   
```  
  
I do not know if it is the right way to do it though.  
Now i can use boost log like c++ glog.  
It is easy to use boost log to write to both console and disk file with logrotation.  
  
The log filename is like this:  
` foo_20240924_220112.711990_CST.log `  
  
log messages are like these:  
```  
2024-09-24 22:01:25.084288 CST [debug  ] main.cpp:11: This is a debug message.  
2024-09-24 22:01:25.084423 CST [info   ] main.cpp:12: This is an info message.  
2024-09-24 22:01:25.084539 CST [warning] main.cpp:13: This is a warning message.  
2024-09-24 22:01:25.084628 CST [error  ] main.cpp:14: This is an error message.  
2024-09-24 22:01:25.085912 CST [fatal  ] main.cpp:15: This is a fatal message.  
2024-09-24 22:01:25.086041 CST [trace  ] main.cpp:10: This is a trace message.  
```  
  
  
```  
$ cat log.cpp  
  
// boost log  
// ../boost_1_86_0/libs/log/example/basic_usage/main.cpp ,  
// ../boost_1_86_0/libs/log/example/rotating_file/main.cpp ,  
// ../boost_1_86_0/libs/log/example/doc/tutorial_fmt_stream.cpp ,  
  
// makefile:  
// CPPFLAGS  = -MMD -MP -I../boost_1_86_0 -DBOOST_LOG_DYN_LINK  
// LDFLAGS   = -L../boost_1_86_0/stage/lib  
// LDLIBS    = -lboost_log -lboost_log_setup -lboost_chrono -lboost_filesystem  
//              -lboost_thread -lboost_atomic  
  
// environment variable:  
// $ LD_LIBRARY_PATH=../boost_1_82_0/stage/lib  ./a.out  
  
#include "log.h"  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/date_time/posix_time/posix_time_types.hpp>  
#include <boost/log/attributes.hpp>  
#include <boost/log/attributes/named_scope.hpp>  
#include <boost/log/attributes/timer.hpp>  
#include <boost/log/common.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/sinks/sync_frontend.hpp>  
#include <boost/log/sinks/text_file_backend.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/sources/record_ostream.hpp>  
#include <boost/log/sources/severity_logger.hpp>  
#include <boost/log/support/date_time.hpp>  
#include <boost/log/trivial.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/utility/setup/console.hpp>  
#include <boost/log/utility/setup/file.hpp>  
#include <string>  
  
namespace attrs = boost::log::attributes;  
namespace expr = boost::log::expressions;  
namespace keywords = boost::log::keywords;  
namespace logging = boost::log;  
namespace sinks = boost::log::sinks;  
namespace src = boost::log::sources;  
  
std::string timezone2() {  
  char str[50];  
  struct tm result;  
  auto clock = time(nullptr);  
  auto timeptr = localtime_r(&clock, &result);  
  strftime(str, sizeof(str), "%Z", timeptr);  
  return str;  
}  
  
void init_logging(const std::string &log_dir, const std::string &name) {  
  const std::string pattern =  
      std::string("_%Y%m%d_%H%M%S.%f_") + timezone2() + ".log";  
  const size_t rotation_size = 1024 * 1024 * 1;  
  const size_t max_files = 5;  
  const size_t min_free_space = 1024ULL * 1024 * 1024 * 10;  
  const size_t severity_len = std::strlen("warning");  
  
  auto format = expr::stream  
                << expr::format_date_time<boost::posix_time::ptime>(  
                       "TimeStamp", "%Y-%m-%d %H:%M:%S.%f")  
                << " " << expr::attr<std::string>("Timezone2") << " ["  
                << std::left << std::setw(severity_len) << std::setfill(' ')  
                << logging::trivial::severity << "] " << expr::smessage;  
  
  auto file_sink = logging::add_file_log(  
      keywords::file_name = log_dir + "/" + name + pattern,  
      keywords::target_file_name = name + pattern,  
      keywords::rotation_size = rotation_size, // in characters  
      keywords::auto_flush = true, keywords::target = log_dir,  
      keywords::max_size = rotation_size * max_files, // in bytes  
      keywords::min_free_space = min_free_space,      // in bytes  
      keywords::max_files = max_files,  
      keywords::filter = logging::trivial::severity >= logging::trivial::trace,  
      keywords::format = format);  
  
  auto console_sink = logging::add_console_log(  
      std::clog,  
      keywords::filter = logging::trivial::severity >= logging::trivial::trace,  
      keywords::format = format);  
  
  logging::core::get()->add_global_attribute(  
      "Timezone2", logging::attributes::make_function<std::string>(&timezone2));  
  
  // Add common attributes like TimeStamp  
  logging::add_common_attributes();  
}  
$  
```  
  
```  
$ cat log.h  
  
#ifndef log_h  
#define log_h  
  
#include <boost/log/trivial.hpp>  
  
// log macro  
#define LOG(severity)                                                          \  
  BOOST_LOG_TRIVIAL(severity) << __FILE__ << ":" << __LINE__ << ": "  
  
void init_logging(const std::string &log_dir, const std::string &name);  
  
#endif  
$  
```  
  
```  
$ cat main.cpp  
  
#include "log.h"  
#include <string>  
  
int main() {  
  const std::string log_dir = "logs"; // get executable directory  
  const std::string name = "foo";  
  init_logging(log_dir, name);  
  
  for (;;) { // test rotation  
    LOG(trace) << "This is a trace message.";  
    LOG(debug) << "This is a debug message.";  
    LOG(info) << "This is an info message.";  
    LOG(warning) << "This is a warning message.";  
    LOG(error) << "This is an error message.";  
    LOG(fatal) << "This is a fatal message.";  
  }  
  return 0;  
}  
$  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-09-24 16:00:05 UTC  
> Url: https://github.com/boostorg/log/issues/235#issuecomment-2371709893  

I suppose, this is in continuation of https://github.com/boostorg/log/issues/212? If so, thanks for reporting back.  
  
Note that `localtime` may not be thread-safe, you should probably use `localtime_r`.

---

## Comment 2

> Username: lijh8  
> Created at: 2024-09-24 16:05:41 UTC  
> Url: https://github.com/boostorg/log/issues/235#issuecomment-2371722726  

Hi Andrey,  
Yes, i finally got some progress on that and started to know your work of this log library a bit more.  
I mostly write single thread code. I will use localtime_r for sure.  
Thanks!
