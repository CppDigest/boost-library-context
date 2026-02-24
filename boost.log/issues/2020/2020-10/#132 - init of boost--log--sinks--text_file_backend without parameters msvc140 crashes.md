# #132 - init of boost::log::sinks::text_file_backend without parameters msvc140 crashes [Closed]

> Username: zaphod71  
> Created at: 2020-10-22 19:26:09 UTC  
> Updated at: 2020-10-23 20:19:17 UTC  
> Closed at: 2020-10-22 21:31:45 UTC  
> Url: https://github.com/boostorg/log/issues/132  

The initialization of a text_file_backend sink without parameters crashes with from version 1_73 and toolset vc140. Toolset vc142 is working.  Version 1_65 is working with both toolsets.  
  
in the example the issue occurs in the init_from_settings() method.  
  
this is working too:  
  
```  
boost::shared_ptr<boost::log::sinks::text_file_backend> text_sink(new boost::log::sinks::text_file_backend(boost::log::keywords::file_name = "file.log", boost::log::keywords::target_file_name = "file.log"));  
```  
  
  
Here the full example:  
  
```  
#include <cstddef>  
#include <string>  
#include <iostream>  
#include <boost/log/sources/severity_logger.hpp>  
#include <boost/log/sources/record_ostream.hpp>  
#include <boost/log/utility/setup.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/utility/setup/settings.hpp>  
#include <boost/log/utility/setup/from_settings.hpp>  
#include <boost/log/utility/setup/filter_parser.hpp>  
#include <boost/log/utility/setup/formatter_parser.hpp>  
#include <boost/regex.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/core.hpp>  
#include <boost/log/sinks.hpp>  
  
  
namespace logging = boost::log;  
namespace src = boost::log::sources;  
  
// Let's define our own severity levels  
enum severity_level  
{  
	normal,  
	notification,  
	warning,  
	error,  
	critical  
};  
  
const char* const severity_strings[] =  
{  
	"normal",  
	"notification",  
	"warning",  
	"error",  
	"critical"  
};  
  
// The operator puts a human-friendly representation of the severity level to the stream  
std::ostream& operator<< (std::ostream& strm, severity_level level)  
{  
	if (static_cast<std::size_t>(level) < sizeof(severity_strings) / sizeof(*severity_strings))  
		strm << severity_strings[level];  
	else  
		strm << static_cast<int>(level);  
  
	return strm;  
}  
  
// The operator parses the severity level from the stream  
std::istream& operator>> (std::istream& strm, severity_level& level)  
{  
	if (strm.good())  
	{  
		std::string str;  
		strm >> str;  
  
		for (unsigned int i = 0; i < sizeof(severity_strings) / sizeof(*severity_strings); ++i)  
		{  
			if (str == severity_strings[i])  
			{  
				level = static_cast<severity_level>(i);  
				return strm;  
			}  
		}  
  
		strm.setstate(std::ios_base::failbit);  
	}  
  
	return strm;  
}  
  
void init_logging()  
{  
	// Before initializing the library from settings, we need to register any custom filter and formatter factories  
	logging::register_simple_filter_factory< severity_level, char >("Severity");  
	logging::register_simple_formatter_factory< severity_level, char >("Severity");  
  
	logging::settings setts;  
  
	setts["Core"]["Filter"] = "%Severity% >= warning";  
	setts["Core"]["DisableLogging"] = false;  
  
	setts["Sinks.File.Filter"] = "%Severity% >= normal";  
	setts["Sinks.File.Destination"] = "TextFile";  
	setts["Sinks.File.FileName"] = "log.log";  
	setts["Sinks.File.TargetFileName"] = "log.log";  
	setts["Sinks.File.Target"] = "c:/temp";  
	setts["Sinks.File.AutoFlush"] = true;  
	setts["Sinks.File.RotationSize"] = 10 * 1024 * 1024; // 10 MiB  
	setts["Sinks.File.Format"] = "%TimeStamp% [%Severity%] %Message%";  
  
	logging::init_from_settings(setts);  
  
	// Add attributes  
	logging::add_common_attributes();  
}  
  
int main(int, char* [])  
{  
	src::severity_logger_mt<severity_level> lg;  
  
	init_logging();  
  
	BOOST_LOG_SEV(lg, error) << "test";  
  
	return 0;  
}  
```

---

## Comment 1

> Username: zaphod71  
> Created at: 2020-10-22 19:48:49 UTC  
> Url: https://github.com/boostorg/log/issues/132#issuecomment-714722825  

I think here is the issue, the paths are not initialized  
  
![screenshot_20201022_213447](https://user-images.githubusercontent.com/64422359/96921360-aaf54d80-14ae-11eb-8e75-af56e80bd7cd.png)  
  
and here the exception happens....  
![screenshot_20201022_213313](https://user-images.githubusercontent.com/64422359/96921357-aa5cb700-14ae-11eb-83cb-1bb07165648f.png)  
  
the result:  
![screenshot_20201022_214654](https://user-images.githubusercontent.com/64422359/96922548-4935e300-14b0-11eb-9056-0bb72cc9c664.png)

---

## Comment 2

> Username: Lastique  
> Created at: 2020-10-22 21:31:45 UTC  
> Url: https://github.com/boostorg/log/issues/132#issuecomment-714774036  

The `construct` arguments are initialized correctly using Boost.Parameter API. I suspect, this may be a compiler bug triggered by changes in [Boost.Parameter](https://github.com/boostorg/parameter) released in 1.71. Or there may be an MSVC-specific bug in Boost.Parameter, which is unlikely, given that the code works with a later version of the compiler. In any case, there's nothing I can do in Boost.Log about it, sorry.

---

## Comment 3

> Username: zaphod71  
> Created at: 2020-10-23 08:07:10 UTC  
> Url: https://github.com/boostorg/log/issues/132#issuecomment-715133894  

Oh, no good news...  
```  
  
	boost::log::add_file_log(  
		boost::log::keywords::file_name = "test.log",  
//		boost::log::keywords::target_file_name = "test.log",  
		boost::log::keywords::rotation_size = 10 * 1024 * 1024,  
		boost::log::keywords::auto_flush = true,  
		boost::log::keywords::time_based_rotation = boost::log::sinks::file::rotation_at_time_point(0, 0, 0)  
	);  
```  
  
This example is working with the toolset vc142, but not with vc140 (current boost sources).  
If the issue is triggered in Boot.Parameter how & where I should address it?

---

## Comment 4

> Username: Lastique  
> Created at: 2020-10-23 08:41:04 UTC  
> Url: https://github.com/boostorg/log/issues/132#issuecomment-715194212  

You can create an issue in [Boost.Parameter](https://github.com/boostorg/parameter) with a small test case. But I would recommend debugging the problem yourself and creating a PR instead as the library is currently not properly maintained. Or upgrade your compiler.

---

## Comment 5

> Username: zaphod71  
> Created at: 2020-10-23 20:19:17 UTC  
> Url: https://github.com/boostorg/log/issues/132#issuecomment-715570757  

FYI  
  
to build the library for toolset vc140 with the preprocessor macro:  
`BOOST_NO_CXX11_RVALUE_REFERENCES`  
solves the issue (the exception occurs in boost.filesystem)  
![screenshot_20201023_221824](https://user-images.githubusercontent.com/64422359/97050497-c3806900-157d-11eb-803e-4057f2b2be36.png)
