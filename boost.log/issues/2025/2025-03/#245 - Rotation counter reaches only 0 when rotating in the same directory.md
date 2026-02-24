# #245 - Rotation counter reaches only 0 when rotating in the same directory [Closed]

> Username: livingsilver94  
> Created at: 2025-03-17 16:17:18 UTC  
> Updated at: 2025-03-17 22:55:07 UTC  
> Closed at: 2025-03-17 22:47:45 UTC  
> Url: https://github.com/boostorg/log/issues/245  

I want to rotate log files **at startup**, not when the application closes.   
The idea is to have `runtime.log` as the _current_ file, and `runtime%N.log` as previous log files, in the same directory, e.g. `./log`. When the application starts, `runtime.log` is renamed into `runtime%N.log` and a new `runtime.log` is created. Obviously, `%N` is replaced with whatever value the counter holds.  
  
I couldn't figure out a way to do that with pure Boost.Log API, so I added a manual `rotate_file()` call if the previous log file (created in a previous execution) exists.  
Now, I said the current and old log files must reside in the same directory. Using the code below as a reproducible example, you can see that the maximum value the counter reaches is... zero. If you instead replace `keywords::target = directory` with, say, `keywords::target = "."`, the counter is able to go beyond zero, but of course the old log files are placed in a different directory which is not what I want to do. I'm not sure whether this is a bug or a misuse of Boost.Log.  
  
You can compile the code below by running `g++ -lboost_filesystem -lboost_log -lboost_date_time -lboost_thread -DBOOST_LOG_DYN_LINK=1 test.cpp`  
  
```cpp  
#include <boost/log/attributes.hpp>  
#include <boost/log/common.hpp>  
#include <boost/log/core.hpp>  
#include <boost/log/exceptions.hpp>  
#include <boost/log/utility/setup/file.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/sinks.hpp>  
#include <boost/log/support/date_time.hpp>  
#include <boost/log/sources/severity_feature.hpp>  
#include <boost/log/sources/severity_logger.hpp>  
#include <filesystem>  
  
using namespace boost::log;  
  
enum class Severity {  
    Verbose = 1,  
    Info = 2,  
    Error = 255,  
};  
  
std::ostream& operator<<(std::ostream& s, Severity sev) {  
    s << (int)sev;  
    return s;  
}  
  
BOOST_LOG_ATTRIBUTE_KEYWORD(timestamp, "Timestamp", attributes::local_clock)  
BOOST_LOG_ATTRIBUTE_KEYWORD(severity, "Severity", Severity)  
BOOST_LOG_ATTRIBUTE_KEYWORD(context, "Context", std::string_view)  
  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(logger, sources::severity_logger_mt<Severity>)  
  
auto initFile(Severity minSev, const std::filesystem::path& directory) -> void  
{  
    namespace exp = expressions;  
  
    // Register the sink backend that writes on files.  
    auto sink = add_file_log(  
        keywords::auto_flush = true,  
        keywords::file_name = directory / "runtime.log",  
        keywords::target_file_name = directory / "runtime%N.log",  
        keywords::enable_final_rotation = false,  
        keywords::open_mode = std::ios_base::app,  
        keywords::target = directory,  
        keywords::format =  
            (exp::stream << "[" << exp::format_date_time<boost::posix_time::ptime>("Timestamp", "%Y-%m-%dT%H:%M:%S")  
                         << "] " << severity << ": "  
  
                         << exp::if_(exp::has_attr(context))[exp::stream << "<" << context << ">: "] << exp::smessage));  
  
    auto core = core::get();  
    core->add_global_attribute("Timestamp", attributes::local_clock());  
    core->set_filter(severity <= minSev);  
  
    sink->locked_backend()->scan_for_files();  
    if (std::filesystem::exists(directory / "runtime.log")) {  
        BOOST_LOG_SEV(logger::get(), Severity::Error) << L"ROTATING AT STARTUP";  
        sink->locked_backend()->rotate_file();  
    }  
}  
  
  
int main(int argc, char** argv) {  
   static const std::filesystem::path directory = "log";  
   initFile(Severity::Error, directory);  
   BOOST_LOG_SEV(logger::get(), Severity::Error) << "TERMINATING";  
}  
```  
  
This was performed on Fedora using GCC with Boost 1.83.0.

---

## Comment 1

> Username: Lastique  
> Created at: 2025-03-17 16:30:51 UTC  
> Url: https://github.com/boostorg/log/issues/245#issuecomment-2730151108  

I'm not sure what exactly you're doing, but if you want to take previously written log files into account (including to start counting the new log files from the last written one), you need to call [`scan_for_files`](https://www.boost.org/doc/libs/1_87_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.text_file.file_scanning) on the sink backend. Try that, and if it doesn't help, please provide a short reproducer that shows the problem.

---

## Comment 2

> Username: livingsilver94  
> Created at: 2025-03-17 20:36:07 UTC  
> Url: https://github.com/boostorg/log/issues/245#issuecomment-2730827642  

I'm sorry, I filed this issue in a moment of despair. I've updated the first message, including your suggestion that doesn't seem to help unfortunately.

---

## Comment 3

> Username: Lastique  
> Created at: 2025-03-17 22:55:05 UTC  
> Url: https://github.com/boostorg/log/issues/245#issuecomment-2731133451  

Thanks for the report and the reproducer. This should be fixed now in develop and hopefully in Boost 1.88.0.  
  
A few comments:  
  
- You actually don't need to explicitly call `scan_for_files` as it is called implicitly by `add_file_log` if it creates a file collector (i.e. `keywords::target` parameter is specified). I initially forgot about this, sorry.  
- In the older Boost releases, before the fix, the issue can be mitigated by writing log files to a location outside the target storage specified in `keywords::target`. That is, both `keywords::file_name` and `keywords::target_file_name` should specify a location outside that in `keywords::target`.
