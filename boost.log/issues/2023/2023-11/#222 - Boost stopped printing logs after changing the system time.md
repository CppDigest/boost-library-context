# #222 - Boost stopped printing logs after changing the system time [Closed]

> Username: yongboye  
> Created at: 2023-11-13 06:32:49 UTC  
> Updated at: 2023-11-14 10:51:30 UTC  
> Closed at: 2023-11-14 10:51:29 UTC  
> Url: https://github.com/boostorg/log/issues/222  

Recently, we encountered a system time change on the server for unknown reasons, and the boost log stopped printing.  
I wrote a simple example to verify this fact, and how to solve this problem  
  
#include <boost/log/core.hpp>  
#include <boost/log/trivial.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/utility/setup/console.hpp>  
#include <boost/log/utility/setup/file.hpp>  
#include <boost/log/sinks/text_file_backend.hpp>  
#include <boost/log/sinks/sync_frontend.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/support/date_time.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
  
namespace logging = boost::log;  
namespace src = boost::log::sources;  
namespace sinks = boost::log::sinks;  
namespace expr = boost::log::expressions;  
namespace attrs = boost::log::attributes;  
namespace keywords = boost::log::keywords;  
  
void init_logging() {  
	// 设置时间戳格式  
	logging::add_common_attributes();  
	logging::core::get()->add_global_attribute("TimeStamp", attrs::local_clock());  
  
	// 创建日志记录器  
	typedef sinks::synchronous_sink<sinks::text_file_backend> file_sink;  
	boost::shared_ptr<file_sink> fsink(new file_sink(  
		keywords::file_name = "sample_%Y%m%d_%H%M%S.log",  
		keywords::rotation_size = 10 * 1024 * 1024,  // 10 MB rotation size  
		keywords::auto_flush = true  
	));  
  
	// 设置文件日志格式  
	fsink->set_formatter(expr::stream  
		<< expr::format_date_time<boost::posix_time::ptime>("TimeStamp", "%Y-%m-%d %H:%M:%S.%f")  
		<< " [" << expr::attr<logging::trivial::severity_level>("Severity") << "] "  
		<< expr::message);  
  
	// 将文件日志添加到日志核心  
	logging::core::get()->add_sink(fsink);  
  
	// 控制台输出  
	logging::add_console_log(std::cout);  
}  
  
  
int main()  
{  
	{  
		// 初始化日志系统  
		init_logging();  
  
		// 写入一些日志  
		src::logger lg;  
  
		while (1) {  
			BOOST_LOG(lg) << "Hello, Boost.Log!";  
			std::this_thread::sleep_for(std::chrono::seconds(3));  
		}  
	}  
}

---

## Comment 1

> Username: Lastique  
> Created at: 2023-11-13 09:00:15 UTC  
> Url: https://github.com/boostorg/log/issues/222#issuecomment-1807711058  

Did you diagnose this problem to pinpoint specifically Boost.Log? That is, are you sure the problem is not elsewhere? For example, your program could be blocking somewhere on a timed wait that takes longer than expected because of the clock change.  
  
If you did debug, please provide more details. Does Boost.Log block somewhere? Or some filter incorrectly discards log records? With synchronous logging, there are no queues that could be piling up log records, and the rest of the code simply formats the record and writes it into the file, and clock changes should not affect it.

---

## Comment 2

> Username: debuggingbug  
> Created at: 2023-11-14 09:40:24 UTC  
> Url: https://github.com/boostorg/log/issues/222#issuecomment-1809861398  

@yongboye 这是微软 STL 的锅，Boost.Log 是无辜的。  
  
The problem is that you use `std::this_thread::sleep_for(std::chrono::seconds(3));`.  
  
It's a terrible thing under MSVC, you should use `boost::this_thread::sleep_for`.  
  
https://github.com/microsoft/STL/issues/3605

---

## Comment 3

> Username: yongboye  
> Created at: 2023-11-14 10:50:08 UTC  
> Url: https://github.com/boostorg/log/issues/222#issuecomment-1809972498  

You're right  
  
  
  
---Original---  
From: ***@***.***&gt;  
Date: Tue, Nov 14, 2023 17:40 PM  
To: ***@***.***&gt;;  
Cc: ***@***.******@***.***&gt;;  
Subject: Re: [boostorg/log] Boost stopped printing logs after changing thesystem time (Issue #222)  
  
  
  
  
   
@yongboye 这是微软 STL 的锅，Boost.Log 是无辜的。  
   
The problem is that you use std::this_thread::sleep_for(std::chrono::seconds(3));.  
   
It's a terrible thing under MSVC, you should use boost::this_thread::sleep_for.  
   
microsoft/STL#3605  
   
—  
Reply to this email directly, view it on GitHub, or unsubscribe.  
You are receiving this because you were mentioned.Message ID: ***@***.***&gt;

---

## Comment 4

> Username: Lastique  
> Created at: 2023-11-14 10:51:29 UTC  
> Url: https://github.com/boostorg/log/issues/222#issuecomment-1809975360  

Thanks.
