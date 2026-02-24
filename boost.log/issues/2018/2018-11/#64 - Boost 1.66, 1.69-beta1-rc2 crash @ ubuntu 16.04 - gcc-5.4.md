# #64 - Boost 1.66, 1.69-beta1-rc2 crash @ ubuntu 16.04 - gcc-5.4 [Closed]

> Username: dineshkumar02  
> Created at: 2018-11-28 15:57:06 UTC  
> Updated at: 2018-12-14 08:22:50 UTC  
> Closed at: 2018-12-14 08:22:50 UTC  
> Url: https://github.com/boostorg/log/issues/64  

Hi,  
  
I am not sure what is the root cause of the ${SUBJECT} problem, and i am not sure which component of boost  
is crashing. From the gdb backtrace, I see the problem is with boost log and I am not sure whether it is the real problem.  
  
I am attaching the back trace file, and in that file it is failing at the file dbActions.cpp:496  
which is my code file, and the content of the file at that line number is  
  
    SLOG( debu ) << "executing query " << query; --> query is (const string&)  
  
But, I am not facing this issue in visual studio 15.8.  
  
Kindly let me know if I miss anything here.  
[back_trace.txt](https://github.com/boostorg/log/files/2625614/back_trace.txt)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-28 16:09:46 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-442503718  

https://gist.github.com/vinniefalco/684511259d6445c4f5e60547af785bc9

---

## Comment 2

> Username: dineshkumar02  
> Created at: 2018-11-28 16:11:49 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-442504502  

Adding more information,  
  
The definition of SLOG is defined as below  
  
```  
// Severity logger  
static src::wseverity_logger<severity_level> logger  
#define SLOG( sv )                                                         \  
	BOOST_LOG_SEV( logger, sv ) << boost::log::add_value( "Line", __LINE__ ) \  
	                            << boost::log::add_value( "File", __FILE__ )  
```  
  
--Dinesh

---

## Comment 3

> Username: Lastique  
> Created at: 2018-11-28 16:25:02 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-442509619  

Please, provide a small reproducer code sample.  
  
From the backtrace I can only see that the crash happens while executing the filter expression on one of the sinks. I can't tell what is wrong from it.

---

## Comment 4

> Username: dineshkumar02  
> Created at: 2018-11-28 18:29:48 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-442554481  

@Lastique, Sure thing.  
  
Tomorrow, I will try to re-produce the problem with a sample code will submit here.

---

## Comment 5

> Username: dineshkumar02  
> Created at: 2018-11-29 18:46:50 UTC  
> Updated at: 2018-11-30 10:25:42 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-442947797  

Hi Team,  
  
I am still trying to debug the root cause of the problem, and unable to find any solution for it. I will continue my investigation on this, and for now I am updating the status here.  
  
As per Peter Dimov(slack channel) suggestions, I tried to investigate this issue with the undefined sanitizer with gcc 6.x, and tried to eliminate the static object initialization problem, without any luck.  
  
I would like to share the code of logger here, and requesting you to suggest me if I am doing anything wrong here.  
  
**logger.h**  
```  
#ifndef INCLUDE_LOGGER_H_  
#define INCLUDE_LOGGER_H_  
#include "alias.h"  
  
enum severity_level { deb=0, info, reply, warn, erro, bug, fatal };  
  
BOOST_LOG_ATTRIBUTE_KEYWORD( severity, "Severity", severity_level )  
BOOST_LOG_ATTRIBUTE_KEYWORD( timestamp, "TimeStamp",attrs::local_clock::value_type)  
BOOST_LOG_ATTRIBUTE_KEYWORD( line, "Line", int )  
BOOST_LOG_ATTRIBUTE_KEYWORD( file, "File", std::string )  
  
class logger {  
	src::severity_logger_mt<severity_level>* log;  
	boost::shared_ptr<sinks::synchronous_sink<sinks::text_file_backend>> log_file_sink;  
	boost::shared_ptr<sinks::synchronous_sink<sinks::text_ostream_backend>> console_log_sink;  
  
public:  
	logger();  
	void init_logging(bool foreground, const string& logfile);  
	void enable_debug(bool foreground);  
	src::severity_logger_mt<severity_level>* get_log();  
};  
```  
  
**logger.cpp**  
```  
#include "../include/logger.h"  
  
extern bool trace;  
extern bool debug;  
  
logger::logger() {  
	log = nullptr;  
	console_log_sink = nullptr;  
	log_file_sink = nullptr;  
}  
  
src::severity_logger_mt<severity_level>* logger::get_log() {  
	return log;  
}  
  
void logger::init_logging( bool foreground, const string& logfile ) {  
  
	log = new src::severity_logger_mt<severity_level>{};  
  
	if ( foreground ) {  
		console_log_sink = logging::add_console_log(  
		  std::clog,  
		  keywords::format =  
		    expr::stream << "[" << expr::format_date_time( timestamp, "%Y-%m-%d %H:%M:%S" ) << "]"  
		                 << expr::if_( expr::has_attr( file ) &&  
		                               trace )[ expr::stream << " "  
		                                                     << "[" << file << ":" ]  
		                 << expr::if_( expr::has_attr( line ) && trace )[ expr::stream << line << "]" ]  
		                 << " "  
		                 << "[" << severity << "]"  
		                 << " " << expr::message );  
  
		if ( debug )  
			console_log_sink->set_filter( severity >= deb );  
		else  
			console_log_sink->set_filter( severity >= info );  
	//	const auto loc = boost::locale::generator()( "en_US.UTF-8" );  
		//console_log_sink->imbue( loc );  
		logging::core::get()->add_sink(console_log_sink);  
	} else {  
		log_file_sink = logging::add_file_log(  
		  logfile,  
		  keywords::format =  
		    expr::stream << "[" << expr::format_date_time( timestamp, "%Y-%m-%d %H:%M:%S.%f" ) << "]"  
		                 << expr::if_( expr::has_attr( file ) &&  
		                               trace )[ expr::stream << " "  
		                                                     << "[" << file << ":" ]  
		                 << expr::if_( expr::has_attr( line ) && trace )[ expr::stream << line << "]" ]  
		                 << " "  
		                 << "[" << severity << "]"  
		                 << " " << expr::message,  
		  keywords::open_mode = std::ios_base::app );  
  
		if ( debug )  
			log_file_sink->set_filter( severity >= deb );  
		else  
			log_file_sink->set_filter( severity >= info );  
		// std::locale loc = boost::locale::generator()( "en_US.UTF-8" );  
		// logFileSink->imbue( loc );  
	logging::core::get()->add_sink(log_file_sink);  
	}  
    logging::core::get()->add_global_attribute("TimeStamp", attrs::local_clock());  
	logging::add_common_attributes();  
}  
  
void logger::enable_debug(bool foreground) {  
	if ( foreground )  
		console_log_sink->set_filter( severity >= deb );  
	else  
		log_file_sink->set_filter( severity >= deb );  
}  
```  
  
**macros**  
  
```  
extern logger* g_logger;  
#define LOG                                                                  \  
	BOOST_LOG_SEV( *(g_logger->get_log()), info ) << boost::log::add_value( "Line", __LINE__ ) \  
	                              << boost::log::add_value( "File", __FILE__ )  
  
#define SLOG( sv )                                                         \  
	BOOST_LOG_SEV( *(g_logger->get_log()), sv ) << boost::log::add_value( "Line", __LINE__ ) \  
	                            << boost::log::add_value( "File", __FILE__ )  
```  
  
Attaching the new back trace file, and undefined sanitizer outputs.  
  
[back_trace2.txt](https://github.com/boostorg/log/files/2630452/back_trace2.txt)  
[undefined_sanitizer.txt](https://github.com/boostorg/log/files/2630453/undefined_sanitizer.txt)

---

## Comment 6

> Username: dineshkumar02  
> Created at: 2018-11-30 04:39:37 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-443088794  

Hi,  
  
I tried the log example code with the sanitizer option, I got the similar errors at the function "logging::add_file_log", but I am unable to reproduce the crash. Even, in my program logger is working fine for few seconds, then it's crashing.  
  
Here is the example, which I've tried.  
https://www.boost.org/doc/libs/1_63_0/libs/log/example/doc/tutorial_fmt_stream.cpp  
  
Any suggestions/inputs would be very helpful to me to make the further progress.  
  
[boost_example_sanitizer.txt](https://github.com/boostorg/log/files/2632046/boost_example_sanitizer.txt)

---

## Comment 7

> Username: Lastique  
> Created at: 2018-11-30 10:50:17 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-443166355  

> I would like to share the code of logger here, and requesting you to suggest me if I am doing anything wrong here.  
  
Nothing fatal jumps at me. A few comments though:  
  
- The `trace` flag is cached within the formatters, it is not read from the external variable every time a log record is formatted. If you expect it to be read, you need to use `phoenix::ref` to save a reference to it in the formatters.  
- Make sure you call `init_logging` and whatever else to initialize `g_logger`, and you do so exactly once.  
  
The UBSan logs are filled with bogus errors, where UBSan is not able to recognize virtual function calls through a pointer to a base class. I don't see real problems there.  
  
My guess is the crash is likely caused by a problem in your code, like an uninitialized pointer or a race condition. I would suggest running your code under valgrind.

---

## Comment 8

> Username: dineshkumar02  
> Created at: 2018-11-30 19:06:32 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-443306581  

Hi @Lastique ,  
  
Thank you so much for your time in evaluating the contents. I somehow bypassed the boost logger errors, I am trying to see any race conditions causing this crash. I tried a lot and still working on it. Now, I am stuck at this place.   
  
```  
#0  do_lookup_x (undef_name=undef_name@entry=0x4245a9 "_ZNSt9basic_iosIcSt11char_traitsIcEEC2Ev", new_hash=new_hash@entry=4120381767, old_hash=old_hash@entry=0x7ffff7fed100, ref=0x405050,   
    result=result@entry=0x7ffff7fed110, scope=0x7ffff7ffe420, i=0, version=0x7ffff7fda780, flags=5, skip=0x0, type_class=1, undef_map=0x7ffff7ffe168) at dl-lookup.c:355  
#1  0x00007ffff7de1b1f in _dl_lookup_symbol_x (undef_name=0x4245a9 "_ZNSt9basic_iosIcSt11char_traitsIcEEC2Ev", undef_map=0x7ffff7ffe168, ref=ref@entry=0x7ffff7fed1c8, symbol_scope=0x7ffff7ffe4c0,   
    version=0x7ffff7fda780, type_class=type_class@entry=1, flags=5, skip_map=0x0) at dl-lookup.c:829  
#2  0x00007ffff7de6ac6 in _dl_fixup (l=<optimized out>, reloc_arg=<optimized out>) at ../elf/dl-runtime.c:111  
#3  0x00007ffff7dee923 in _dl_runtime_resolve_avx () at ../sysdeps/x86_64/dl-trampoline.h:112  
#4  0x000000000000000b in ?? ()  
#5  0x00000000009c74d0 in ?? ()  
#6  0x00007ffff5621184 in __GI___libc_malloc (bytes=140737354060704) at malloc.c:2913  
#7  0xffff800008012aa1 in ?? ()  
#8  0x00007ffff7fed55f in ?? ()  
#9  0xc23cd393fa2e2500 in ?? ()  
#10 0x00007ffff7fed5a7 in ?? ()  
#11 0x00007ffff7bc518a in pqParseInput3 () from /home/dinesh/workspace/deps/pg/lib/libpq.so.5  
#12 0x000000000043d798 in boost::operators_impl::equality_comparable1<node_id_type, boost::operators_impl::totally_ordered2<node_id_type, unsigned long, boost::operators_impl::operators_detail::empty_base<node_id_type> > >::equality_comparable1 (this=<error reading variable: Cannot access memory at address 0xf7fed5f8>) at /home/dinesh/workspace/deps/boost/include/boost/operators.hpp:160  
Backtrace stopped: previous frame inner to this frame (corrupt stack?)  
```

---

## Comment 9

> Username: dineshkumar02  
> Created at: 2018-12-14 08:22:40 UTC  
> Url: https://github.com/boostorg/log/issues/64#issuecomment-447251099  

Hi Everyone,  
  
Thank you very much for your guidance on this. The root cause of the problem is "not having enough thread stack size", which I was configuring for each thread. I configured the threads to run with "10 bytes", and I was assuming the stack size which I have configured was 10KB.  
  
Issue is resolved with the right configuration, hence closing this case.  
  
Regards,  
Dinesh
