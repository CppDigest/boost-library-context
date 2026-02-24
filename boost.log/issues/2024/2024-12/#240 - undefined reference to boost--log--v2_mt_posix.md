# #240 - undefined reference to boost::log::v2_mt_posix [Closed]

> Username: Monroe07  
> Created at: 2024-12-03 11:14:38 UTC  
> Updated at: 2024-12-05 09:21:30 UTC  
> Closed at: 2024-12-05 09:21:29 UTC  
> Url: https://github.com/boostorg/log/issues/240  

Boost version: 1.85  
Environment: Cross-compiling on Visual Studio 2022 for Raspberry Pi 5 - Rasbian Bookworm, released 2024-11-19  
  
Disclaimer: I had Boost.log working before, but due to a corrupted SD card, lost working environment, so I'm starting from scratch with little to no idea of what combination of versions/setup process/what I did to make it work before.   
  
At the moment, Attempting to run the example below results in 22 errors, all regarding undefined references to boost::log::v2_mt_posix::*   
I do know that my previous working version used the "#define BOOST_LOG_DYN_LINK 1" which solved this problem back then, but its not working this time.  
  
At the Moment, I'm attempting to get this basic example working:  
`/*  
 *          Copyright Andrey Semashev 2007 - 2013.  
 * Distributed under the Boost Software License, Version 1.0.  
 *    (See accompanying file LICENSE_1_0.txt or copy at  
 *          http://www.boost.org/LICENSE_1_0.txt)  
 */  
  
 //[ example_tutorial_trivial  
#include <boost/log/trivial.hpp>  
  
int main(int, char* [])  
{  
    BOOST_LOG_TRIVIAL(trace) << "A trace severity message";  
    BOOST_LOG_TRIVIAL(debug) << "A debug severity message";  
    BOOST_LOG_TRIVIAL(info) << "An informational severity message";  
    BOOST_LOG_TRIVIAL(warning) << "A warning severity message";  
    BOOST_LOG_TRIVIAL(error) << "An error severity message";  
    BOOST_LOG_TRIVIAL(fatal) << "A fatal severity message";  
  
    return 0;  
}  
//]  
  
Here is the complete list of errors I'm getting:  
Severity	Code	Description	Project	File	Line	Suppression State  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o: in function `main':	Project HUD v2	C:\usr\bin\ld	1	  
Error		undefined reference to `boost::log::v2_mt_posix::trivial::logger::get()'	Project HUD v2	C:\Users\KAMon\Documents\GitHub\Project HUD v2\main.cpp	14	  
Error		/home/pi/projects/Project HUD v2/main.cpp:14: undefined reference to `boost::log::v2_mt_posix::trivial::logger::get()'	Project HUD v2	C:\usr\bin\ld	1	  
Error		/home/pi/projects/Project HUD v2/main.cpp:15: undefined reference to `boost::log::v2_mt_posix::trivial::logger::get()'	Project HUD v2	C:\usr\bin\ld	1	  
Error		/home/pi/projects/Project HUD v2/main.cpp:15: undefined reference to `boost::log::v2_mt_posix::trivial::logger::get()'	Project HUD v2	C:\usr\bin\ld	1	  
Error		/home/pi/projects/Project HUD v2/main.cpp:16: undefined reference to `boost::log::v2_mt_posix::trivial::logger::get()'	Project HUD v2	C:\usr\bin\ld	1	  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o:/home/pi/projects/Project HUD v2/main.cpp:16: more undefined references to `boost::log::v2_mt_posix::trivial::logger::get()' follow	Project HUD v2	C:\usr\bin\ld	1	  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o: in function `boost::log::v2_mt_posix::record::reset()':	Project HUD v2	C:\usr\bin\ld	1	  
Error		undefined reference to `boost::log::v2_mt_posix::record_view::public_data::destroy(boost::log::v2_mt_posix::record_view::public_data const*)'	Project HUD v2	C:\usr\local\include\boost\log\core\record.hpp	157	  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o: in function `boost::log::v2_mt_posix::record boost::log::v2_mt_posix::sources::basic_composite_logger<char, boost::log::v2_mt_posix::sources::severity_logger_mt<boost::log::v2_mt_posix::trivial::severity_level>, boost::log::v2_mt_posix::sources::multi_thread_model<boost::log::v2_mt_posix::aux::light_rw_mutex>, boost::log::v2_mt_posix::sources::features<boost::log::v2_mt_posix::sources::severity<boost::log::v2_mt_posix::trivial::severity_level> > >::open_record<boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<boost::log::v2_mt_posix::keywords::tag::severity, boost::log::v2_mt_posix::trivial::severity_level const> > >(boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<boost::log::v2_mt_posix::keywords::tag::severity, boost::log::v2_mt_posix::trivial::severity_level const> > const&)':	Project HUD v2	C:\usr\bin\ld	1	  
Error		undefined reference to `boost::log::v2_mt_posix::core::get_logging_enabled() const'	Project HUD v2	C:\usr\local\include\boost\log\sources\basic_logger.hpp	460	  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o: in function `boost::log::v2_mt_posix::aux::record_pump<boost::log::v2_mt_posix::sources::severity_logger_mt<boost::log::v2_mt_posix::trivial::severity_level> >::record_pump(boost::log::v2_mt_posix::sources::severity_logger_mt<boost::log::v2_mt_posix::trivial::severity_level>&, boost::log::v2_mt_posix::record&)':	Project HUD v2	C:\usr\bin\ld	1	  
Error		undefined reference to `boost::log::v2_mt_posix::aux::stream_provider<char>::allocate_compound(boost::log::v2_mt_posix::record&)'	Project HUD v2	C:\usr\local\include\boost\log\sources\record_ostream.hpp	508	  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o: in function `boost::log::v2_mt_posix::aux::record_pump<boost::log::v2_mt_posix::sources::severity_logger_mt<boost::log::v2_mt_posix::trivial::severity_level> >::auto_release::~auto_release()':	Project HUD v2	C:\usr\bin\ld	1	  
Error		undefined reference to `boost::log::v2_mt_posix::aux::stream_provider<char>::release_compound(boost::log::v2_mt_posix::aux::stream_provider<char>::stream_compound*)'	Project HUD v2	C:\usr\local\include\boost\log\sources\record_ostream.hpp	493	  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o: in function `boost::log::v2_mt_posix::sources::aux::severity_level<boost::log::v2_mt_posix::trivial::severity_level>::set_value(boost::log::v2_mt_posix::trivial::severity_level)':	Project HUD v2	C:\usr\bin\ld	1	  
Error		undefined reference to `boost::log::v2_mt_posix::sources::aux::get_severity_level()'	Project HUD v2	C:\usr\local\include\boost\log\sources\severity_feature.hpp	136	  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o: in function `boost::log::v2_mt_posix::record boost::log::v2_mt_posix::sources::basic_logger<char, boost::log::v2_mt_posix::sources::severity_logger_mt<boost::log::v2_mt_posix::trivial::severity_level>, boost::log::v2_mt_posix::sources::multi_thread_model<boost::log::v2_mt_posix::aux::light_rw_mutex> >::open_record_unlocked<boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<boost::log::v2_mt_posix::keywords::tag::severity, boost::log::v2_mt_posix::trivial::severity_level const> > >(boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<boost::log::v2_mt_posix::keywords::tag::severity, boost::log::v2_mt_posix::trivial::severity_level const> > const&)':	Project HUD v2	C:\usr\bin\ld	1	  
Error		undefined reference to `boost::log::v2_mt_posix::core::open_record(boost::log::v2_mt_posix::attribute_set const&)'	Project HUD v2	C:\usr\local\include\boost\log\sources\basic_logger.hpp	260	  
Error		/home/pi/projects/Project HUD v2/obj/ARM64/Debug/main.o: in function `boost::log::v2_mt_posix::core::push_record(boost::log::v2_mt_posix::record&&)':	Project HUD v2	C:\usr\bin\ld	1	  
Error		undefined reference to `boost::log::v2_mt_posix::core::push_record_move(boost::log::v2_mt_posix::record&)'	Project HUD v2	C:\usr\local\include\boost\log\core\core.hpp	308	  
Error		ld returned 1 exit status	Project HUD v2	C:\Users\KAMon\Documents\GitHub\Project HUD v2\collect2	1

---

## Comment 1

> Username: Lastique  
> Created at: 2024-12-03 15:07:54 UTC  
> Updated at: 2024-12-03 15:08:34 UTC  
> Url: https://github.com/boostorg/log/issues/240#issuecomment-2514831400  

You are either not linking with Boost.Log (i.e. missing `-lboost_log` in the linker command line) or you are linking with the static Boost.Log library while `BOOST_LOG_DYN_LINK` or `BOOST_ALL_DYN_LINK` is defined. Check your linker command line and also verify which version of the library you're linking with. Pay attention to the library paths specified in the linker command line.

---

## Comment 2

> Username: Monroe07  
> Created at: 2024-12-05 09:21:29 UTC  
> Url: https://github.com/boostorg/log/issues/240#issuecomment-2519724743  

**Got it sorted. I'll detail what I did for anyone who hits this same issue (including potentially me in the future haha] using Visual studio and cross-compiling using the Raspberry Pi project template.**  
  
I wiped and re-installed the Pi OS, re-followed the youtube video [https://www.youtube.com/watch?v=CP_U4sb75cM&t=5s](here), put the resulting boost folder with headers (subfolders with .hpp files in them) and lib folder (with shared objects .o/.a) into a folder ("boost_compile_folder") that I later used in visual studio. In visual studio, project properties put the following, after every change hitting "apply button":  
  
0. Folder structure after movement was something like this:  
boost_compile_folder  
|  
- boost  
------ accumulators  
------ algorithm  
------ align  
------ .  
------ .  
------ .  
------ log  
------ .  
------ .  
------ yap  
------ .  
------ .  
------  [_A bunch of .hpp files_]   
- lib  
------ libboost_atomic.a  
------ libboost_atomic.so  
------ libboost_atomic..so.1.86.0  
------ .  
------ .  
------ . [_A LOT of other similar files_]  
  
  
1. Configuration Properties > VC++ Directories > Include Directories = path to "boost_compile_folder" folder that contains a "boost" folder that has sub-folders named for each boost library feature with hpp files for the feature. _All include directives start with "boost/[path or hpp file to include as per boost getting started page instructions]_  
  
2. Configuration Properties > Linker > General > Additional Library Directories = path to "boost_compile_folder"/lib which contains all shared objects for boost.  
  
3. Configuration Properties > Linker > Input = boost_log;boost_filesystem;boost_thread  
  
4. put "#define BOOST_LOG_DYN_LINK 1" above the include "#include <boost/log/trivial.hpp>" to use shared libraries  
  
5. On the Pi, added the line "export LD_LIBRARY_PATH="/home/pi/boost_compile_folder/lib"" at the end of .bashrc so when running produced .out file from command line, the .out would find the shared libraries used for compilation/linking.
