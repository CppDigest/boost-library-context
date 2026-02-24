# #169 - Unable to see messages properly in event viewer [Open]

> Username: humayunkhan  
> Created at: 2021-12-06 12:33:22 UTC  
> Updated at: 2022-05-04 08:34:51 UTC  
> Url: https://github.com/boostorg/log/issues/169  

Hi,  
  
Following are my environment details:  
I am using Boost Library version 1.77.0  
I am using Windows 10 operating system.  
I am using Visual Studio 2019.  
The platform toolset is v142  
C++ language standard: ISO C++ 17 (/std:c++17)  
C Language standard: ISO C 17 (2018) (/std:c17)  
My project is a DLL project where I am using event logging  
I am also using the following macro: #define BOOST_ALL_DYN_LINK  
  
I am using custom event logging as described in the following link:  
https://www.boost.org/doc/libs/1_77_0/libs/log/example/event_log/main.cpp  
  
I have created a custom message configuration file with the following content:  
`  
; /* --------------------------------------------------------  
; HEADER SECTION  
; */  
SeverityNames=(Debug=0x0:AUTO_SCORE_SEVERITY_DEBUG  
            Info=0x0:AUTO_SCORE_SEVERITY_INFO  
            Warning=0x2:AUTO_SCORE_SEVERITY_WARNING  
            Error=0x1:AUTO_SCORE_SEVERITY_ERROR  
            )  
  
; /* --------------------------------------------------------  
; MESSAGE DEFINITION SECTION  
; */  
  
MessageIdTypedef=WORD  
  
MessageId=0x1  
SymbolicName=CATEGORY_DEBUG  
Language=English  
Debug Message  
.  
  
MessageId=0x2  
SymbolicName=CATEGORY_INFO  
Language=English  
Info Message  
.  
  
MessageId=0x3  
SymbolicName=CATEGORY_WARNING  
Language=English  
Warning Message  
.  
  
MessageId=0x4  
SymbolicName=CATEGORY_ERROR  
Language=English  
Error Message  
.  
  
MessageIdTypedef=DWORD  
  
MessageId=0x100  
Severity=Debug  
Facility=Application  
SymbolicName=DEBUG_MSG  
Language=English  
[%1][%2]: %3  
.  
  
MessageId=0x101  
Severity=Info  
Facility=Application  
SymbolicName=INFO_MSG  
Language=English  
.  
  
MessageId=0x102  
Severity=Warning  
Facility=Application  
SymbolicName=WARNING_MSG  
Language=English  
[%1][%2]: %3  
.  
  
MessageId=0x103  
Severity=Error  
Facility=Application  
SymbolicName=ERROR_MSG  
Language=English  
[%1][%2]: %3  
.  
  
`  
  
The logging header file looks like this:  
`  
#pragma once  
#include <stdexcept>  
#include <string>  
#include <iostream>  
#include <boost/smart_ptr/shared_ptr.hpp>  
#include <boost/date_time/posix_time/posix_time_types.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/date_time/posix_time/posix_time_io.hpp>  
  
#include <boost/log/common.hpp>  
#include <boost/log/attributes.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/sinks/sync_frontend.hpp>  
#include <boost/log/sinks/event_log_backend.hpp>  
#include "autoscore_eventlog_provider/autoscore_eventlog_provider.h"  
  
// Check windows  
#if _WIN32 || _WIN64  
#if _WIN64  
#define ENVIRONMENT64  
#else  
#define ENVIRONMENT32  
#endif  
#endif  
  
// Check GCC  
#if __GNUC__  
#if __x86_64__ || __ppc64__  
#define ENVIRONMENT64  
#else  
#define ENVIRONMENT32  
#endif  
#endif  
  
#if !defined(WIN32_LEAN_AND_MEAN)  
#define WIN32_LEAN_AND_MEAN  
#endif  
  
#include <windows.h>  
  
namespace autoscore::log  
{  
	namespace logging = boost::log;  
	namespace attrs = boost::log::attributes;  
	namespace src = boost::log::sources;  
	namespace sinks = boost::log::sinks;  
	namespace expr = boost::log::expressions;  
	namespace keywords = boost::log::keywords;  
	  
	// Define application-specific severity levels  
	enum severity_level  
	{  
		debug,  
		info,  
		warning,  
		error  
	};  
  
	//Define AutoScore Logging Config  
	struct config  
	{  
		severity_level min_logging_level;  
		std::string message_file;  
		std::string log_name;  
		std::string log_source;  
		bool enable_win_event_log;  
	};  
  
	void init_logging(config logging_config);  
  
	//[ example_sinks_event_log_facilities  
	BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(event_logger, src::severity_logger_mt< severity_level >)  
  
	  
	/// <summary>  
	/// The function raises an event with a debug message  
	/// </summary>  
	/// <param name="message"></param>  
	/// <param name="session_id"></param>  
	void log_debug(std::string const& message, std::string const& session_id = "N/A");  
	  
	/// <summary>  
	/// The function raises an event with a info message  
	/// </summary>  
	/// <param name="message"></param>  
	/// <param name="session_id"></param>  
	void log_info(std::string const& message, std::string const& session_id = "N/A");  
  
	  
	/// <summary>  
	/// The function raises an event with a warning message  
	/// </summary>  
	/// <param name="message"></param>  
	/// <param name="session_id"></param>  
	void log_warning(std::string const& message, std::string const& session_id = "N/A");  
  
	  
	/// <summary>  
	/// The function raises an event with a error message  
	/// </summary>  
	/// <param name="message"></param>  
	/// <param name="session_id"></param>  
	void log_error(std::string const& message, std::string const& session_id = "N/A");  
  
}  
`  
  
The logging implementation file looks like this:  
`  
#include "pch.h"  
#include "autoscore_log.h"  
  
namespace autoscore::log  
{  
    void init_win_event_log(config const logging_config)  
    {  
        // Create an event log sink  
        boost::shared_ptr< sinks::event_log_backend > backend(  
            new sinks::event_log_backend((  
                keywords::message_file = logging_config.message_file, //%APPDATA%\Holberg EEG AS\autoSCORE\Logs\autoscore_eventlog_provider.dll  
                keywords::log_name = logging_config.log_name, //Holberg EEG AS  
                keywords::log_source = logging_config.log_source //autoSCORE  
                ))  
        );  
  
        // Create an event composer. It is initialized with the event identifier mapping.  
        sinks::event_log::event_composer composer(  
            sinks::event_log::direct_event_id_mapping< int >("EventID"));  
  
        // For each event described in the message file, set up the insertion string formatters  
        composer[DEBUG_MSG]  
            % expr::attr< std::string >("TimeStamp")  
            % expr::attr< std::string >("SessionID")  
            % expr::attr< std::string >("Message");  
  
        composer[WARNING_MSG]  
            % expr::attr< std::string >("TimeStamp")  
            % expr::attr< std::string >("SessionID");  
        composer[ERROR_MSG]  
            % expr::attr< std::string >("TimeStamp")  
            % expr::attr< std::string >("SessionID");  
  
        // Then put the composer to the backend  
        backend->set_event_composer(composer);  
  
        auto severity = boost::log::expressions::attr<severity_level>("Severity");  
  
        // We'll have to map our custom levels to the event log event types  
        sinks::event_log::custom_event_type_mapping< severity_level > type_mapping("Severity");  
        type_mapping[debug] = sinks::event_log::make_event_type(AUTO_SCORE_SEVERITY_DEBUG);  
        type_mapping[info] = sinks::event_log::make_event_type(AUTO_SCORE_SEVERITY_INFO);  
        type_mapping[warning] = sinks::event_log::make_event_type(AUTO_SCORE_SEVERITY_WARNING);  
        type_mapping[error] = sinks::event_log::make_event_type(AUTO_SCORE_SEVERITY_ERROR);  
  
        backend->set_event_type_mapper(type_mapping);  
  
        // Usually event categories can be restored by the event identifier.  
        sinks::event_log::custom_event_category_mapping< int > cat_mapping("EventID");  
        cat_mapping[DEBUG_MSG] = sinks::event_log::make_event_category(CATEGORY_DEBUG);  
        cat_mapping[INFO_MSG] = sinks::event_log::make_event_category(CATEGORY_INFO);  
        cat_mapping[WARNING_MSG] = sinks::event_log::make_event_category(CATEGORY_WARNING);  
        cat_mapping[ERROR_MSG] = sinks::event_log::make_event_category(CATEGORY_ERROR);  
  
        backend->set_event_category_mapper(cat_mapping);  
  
        // Create the frontend for the sink  
        boost::shared_ptr< sinks::synchronous_sink< sinks::event_log_backend > > sink(  
            new sinks::synchronous_sink< sinks::event_log_backend >(backend));  
  
        // Set up filter to pass only records that have the necessary attribute  
        sink->set_filter(expr::has_attr< int >("EventID"));  
        sink->set_filter(severity >= logging_config.min_logging_level);  
  
        logging::core::get()->add_sink(sink);  
    }  
  
    void init_logging(config const logging_config)  
    {  
        if (logging_config.enable_win_event_log)  
        {  
            init_win_event_log(logging_config);  
        }  
    }  
  
    void log_debug(std::string const& message, std::string const& session_id)  
    {  
        auto time_stamp = boost::posix_time::to_simple_string(boost::posix_time::microsec_clock::local_time());  
        BOOST_LOG_SCOPED_THREAD_TAG("EventID", (int)DEBUG_MSG);  
        BOOST_LOG_SCOPED_THREAD_TAG("SessionID", session_id);  
        BOOST_LOG_SCOPED_THREAD_TAG("TimeStamp", time_stamp);  
        BOOST_LOG_SCOPED_THREAD_TAG("Message", message);  
        BOOST_LOG_SEV(event_logger::get(), debug) << "[" << time_stamp << "][" << session_id << "]: " << message;  
    }  
	  
    void log_info(std::string const& message, std::string const& session_id)  
    {  
        auto time_stamp = boost::posix_time::to_simple_string(boost::posix_time::microsec_clock::local_time());  
        BOOST_LOG_SCOPED_THREAD_TAG("EventID", (int)INFO_MSG);  
        BOOST_LOG_SCOPED_THREAD_TAG("SessionID", session_id);  
        BOOST_LOG_SCOPED_THREAD_TAG("TimeStamp", time_stamp);  
        BOOST_LOG_SCOPED_THREAD_TAG("Message", message);  
        BOOST_LOG_SEV(event_logger::get(), info) << message;  
    }  
  
    void log_warning(std::string const& message, std::string const& session_id)  
    {  
        auto time_stamp = boost::posix_time::to_simple_string(boost::posix_time::microsec_clock::local_time());  
        BOOST_LOG_SCOPED_THREAD_TAG("EventID", (int)WARNING_MSG);  
        BOOST_LOG_SCOPED_THREAD_TAG("SessionID", session_id);  
        BOOST_LOG_SCOPED_THREAD_TAG("TimeStamp", time_stamp);  
        BOOST_LOG_SEV(event_logger::get(), warning) << message;  
    }  
  
    void log_error(std::string const& message, std::string const& session_id)  
    {  
        auto time_stamp = boost::posix_time::to_simple_string(boost::posix_time::microsec_clock::local_time());  
        BOOST_LOG_SCOPED_THREAD_TAG("EventID", (int)ERROR_MSG);  
        BOOST_LOG_SCOPED_THREAD_TAG("SessionID", session_id);  
        BOOST_LOG_SCOPED_THREAD_TAG("TimeStamp", time_stamp);  
        BOOST_LOG_SEV(event_logger::get(), error) << message;  
    }  
}  
  
`  
  
I have created the header file and definitions DLL based on the *.mc file and created the registry keys by running the DLL consumer application as administrator.  
  
I am able to see the events in the event viewer but without any content. I am attaching screen shots of how it looks like.  
  
I am not sure what is my mistake. Can some one help m  
![event_log_error](https://user-images.githubusercontent.com/763401/144846722-0eb411aa-1128-4ab7-8bc8-e38bdb697919.png)  
e?

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-06 19:14:34 UTC  
> Url: https://github.com/boostorg/log/issues/169#issuecomment-987098038  

The message `INFO_MSG` in the .mc file does not have a template string. Also, `AUTO_SCORE_SEVERITY_INFO` has the same value as `AUTO_SCORE_SEVERITY_DEBUG`.  
  
You're filling `type_mapping` incorrectly. The mapping is supposed to map your attribute values to `event_type`, not the other way around.  
  
The second `sink->set_filter` call override the first one - only the last filter is being set.  
  
The attribute you set in the `BOOST_LOG_SCOPED_THREAD_TAG("Message", message);` lines is overriden by the "Message" attribute that is set by the logger. That attribute is composed by the streaming expression in the logging statement.  
  
The above may or may not be the source of the problem, but still worth fixing. As for the problem with the messages, from my past memory, this is usually related to issues with .dll registration (e.g. the .dll with message resources is not registered or not found where it was registered). `event_log_backend` by default registers the message dll on demand (i.e. if it was not registered before), which means if the dll was moved or deleted, the registry will not be updated. Try `keywords::registration = event_log::forced`.

---

## Comment 2

> Username: humayunkhan  
> Created at: 2021-12-07 15:56:47 UTC  
> Url: https://github.com/boostorg/log/issues/169#issuecomment-988057925  

I corrected the `sink->set_filter` as follows:  
`sink->set_filter(expr::has_attr< int >("EventID") && severity >= logging_config.min_logging_level);`  
  
I have cross checked the possibility of DLL registration issues but everything looks OK there. I even tried using `keywords::registration = event_log::forced` but that did not help.  
  
I have removed the lines in my source code where I was setting the "Message" attribute.  
  
I have added the template string for `INFO_MSG` in the .mc file (actually I removed it earlier to experiment with it)  
  
Regarding the `type_mapping` and the AUTO_SCORE_SEVERITY_INFO, AUTO_SCORE_SEVERITY_DEBUG having same value in the .mc file:  
  
- I am using boost version 1.77.0 so I am following the example here: https://www.boost.org/doc/libs/1_77_0/libs/log/example/event_log/main.cpp  
- I did not fully understand your comment about `type_mapping`; could you please give me an example of a change that I can make?  
- I tried making changes to my .mc file for severity values as follows:  
```  
SeverityNames=(	Debug=0x0:AUTOSCORE_SEVERITY_DEBUG  
				Info=0x1:AUTOSCORE_SEVERITY_INFO  
				Warning=0x2:AUTOSCORE_SEVERITY_WARNING  
				Error=0x3:AUTOSCORE_SEVERITY_ERROR  
			)  
```  
but if I try to use these values in type_mapping the way I have it already, I get a runtime error at the following statement:  
`type_mapping[error] = sinks::event_log::make_event_type(AUTOSCORE_SEVERITY_ERROR);`  
  
this is the error:  
```  
libs\log\src\windows\event_log_backend.cpp(58): Throw in function enum boost::log::v2_mt_nt6::sinks::event_log::event_type __cdecl boost::log::v2_mt_nt6::sinks::event_log::make_event_type(unsigned short)  
Dynamic exception type: struct boost::wrapexcept<class std::out_of_range>  
std::exception::what: Windows NT event type is out of range  
```  
this is because make_event expects only enum event_type values which does not have the value "3" defined.  
that is the actual reason why I had repeated values in the .mc file for severity values.  
  
- I also tried the following type_mapping:  
       ```  
 type_mapping[debug] = sinks::event_log::info;  
        type_mapping[info] = sinks::event_log::info;  
        type_mapping[warning] = sinks::event_log::warning;  
        type_mapping[error] = sinks::event_log::error;  
```  
but even this did not work.  
  
I am kind of stuck. Can you help me out?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-12-07 19:15:51 UTC  
> Updated at: 2021-12-07 19:16:13 UTC  
> Url: https://github.com/boostorg/log/issues/169#issuecomment-988199067  

> I did not fully understand your comment about `type_mapping`; could you please give me an example of a change that I can make?  
  
`type_mapping` is supposed to convert an attribute in the log record to an `event_type` value. So, if you want to convert `severity_level` to an event type, you need something like this:  
  
```  
// We'll have to map our custom levels to the event log event types  
sinks::event_log::custom_event_type_mapping< severity_level > type_mapping("Severity");  
type_mapping[debug] = sinks::event_log::event_type::success;  
type_mapping[info] = sinks::event_log::event_type::info;  
type_mapping[warning] = sinks::event_log::event_type::warning;  
type_mapping[error] = sinks::event_log::event_type::error;  
```  
  
> I tried making changes to my .mc file for severity values as follows:  
>  
> ```  
> SeverityNames=(	Debug=0x0:AUTOSCORE_SEVERITY_DEBUG  
>				Info=0x1:AUTOSCORE_SEVERITY_INFO  
>				Warning=0x2:AUTOSCORE_SEVERITY_WARNING  
>				Error=0x3:AUTOSCORE_SEVERITY_ERROR  
>			)  
> ```  
  
Yes, that's correct. The incorrect part was trying to use the severity constants as event types. As was said in the other issue, those are different things, don't mix them.  
  
If the dll with message resources is registered correctly and accessible then I don't have any specific ideas. The event is clearly sent, and it has the expected type and id. The problem is Windows not translating the id to the message text. A few things to check:  
  
- Check that the insertion strings are being composed correctly e.g. by stepping into `basic_event_log_backend< CharT >::consume` in the debugger.  
- Perhaps the problem is that the system language is different from the language in the message file?  
- Perhaps there's some sort of permissions problem?  
- Does `simple_event_log_backend` work?  
  
I'm not using Windows or event log at all, so I won't be able to help much, sorry. Let me know if you find out what the issue is.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-05-02 16:35:31 UTC  
> Url: https://github.com/boostorg/log/issues/169#issuecomment-1115102560  

Any updates? Is this still relevant?

---

## Comment 5

> Username: humayunkhan  
> Created at: 2022-05-04 08:34:51 UTC  
> Url: https://github.com/boostorg/log/issues/169#issuecomment-1117057438  

> Any updates? Is this still relevant?  
  
I wasn't able to get it working.  
  
I switched to simple_event_log_backend and living with it for now.
