# #156 - "rotating text file" log example error [Closed]

> Username: LFRS  
> Created at: 2021-07-23 09:20:48 UTC  
> Updated at: 2021-07-26 14:35:17 UTC  
> Closed at: 2021-07-23 12:27:57 UTC  
> Url: https://github.com/boostorg/log/issues/156  

Hi,  
  
I'm trying to run the "rotating text file" log example in ubuntu 18.04 using Boost 1.76.0,  
the compilation is done successfully but when I try to run the code it gives me the following error:  
  
FAILURE: boost::filesystem::create_directories: Function not implemented: "/test/build/logs"  
  
The code is:  
  
```  
/*  
 *          Copyright Andrey Semashev 2007 - 2015.  
 * Distributed under the Boost Software License, Version 1.0.  
 *    (See accompanying file LICENSE_1_0.txt or copy at  
 *          http://www.boost.org/LICENSE_1_0.txt)  
 */  
/*!  
 * \file   main.cpp  
 * \author Andrey Semashev  
 * \date   26.04.2008  
 *  
 * \brief  An example of logging into a rotating text file.  
 *         See the library tutorial for expanded comments on this code.  
 *         It may also be worthwhile reading the Wiki requirements page:  
 *         http://www.crystalclearsoftware.com/cgi-bin/boost_wiki/wiki.pl?Boost.Logging  
 */  
  
//#define BOOST_LOG_DYN_LINK 1  
  
#include <stdexcept>  
#include <string>  
#include <iostream>  
#include <boost/smart_ptr/shared_ptr.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
  
#include <boost/log/common.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/attributes.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/sinks/sync_frontend.hpp>  
#include <boost/log/sinks/text_file_backend.hpp>  
  
namespace logging = boost::log;  
namespace attrs = boost::log::attributes;  
namespace src = boost::log::sources;  
namespace sinks = boost::log::sinks;  
namespace expr = boost::log::expressions;  
namespace keywords = boost::log::keywords;  
  
using boost::shared_ptr;  
  
enum { LOG_RECORDS_TO_WRITE = 10000 };  
  
int main(int argc, char* argv[])  
{  
    try  
    {  
        // Create a text file sink  
        typedef sinks::synchronous_sink< sinks::text_file_backend > file_sink;  
        shared_ptr< file_sink > sink(new file_sink(  
            keywords::file_name = "file.log",                       // file name pattern  
            keywords::target_file_name = "%Y%m%d_%H%M%S_%5N.log",   // file name pattern  
            keywords::rotation_size = 16384                         // rotation size, in characters  
            ));  
  
        // Set up where the rotated files will be stored  
        sink->locked_backend()->set_file_collector(sinks::file::make_collector(  
            keywords::target = "logs",                              // where to store rotated files  
            keywords::max_size = 16 * 1024 * 1024,                  // maximum total size of the stored files, in bytes  
            keywords::min_free_space = 100 * 1024 * 1024,           // minimum free space on the drive, in bytes  
            keywords::max_files = 512                               // maximum number of stored files  
            ));  
  
        // Upon restart, scan the target directory for files matching the file_name pattern  
        sink->locked_backend()->scan_for_files();  
  
        sink->set_formatter  
        (  
            expr::format("%1%: [%2%] - %3%")  
                % expr::attr< unsigned int >("RecordID")  
                % expr::attr< boost::posix_time::ptime >("TimeStamp")  
                % expr::smessage  
        );  
  
        // Add it to the core  
        logging::core::get()->add_sink(sink);  
  
        // Add some attributes too  
        logging::core::get()->add_global_attribute("TimeStamp", attrs::local_clock());  
        logging::core::get()->add_global_attribute("RecordID", attrs::counter< unsigned int >());  
  
        // Do some logging  
        src::logger lg;  
        for (unsigned int i = 0; i < LOG_RECORDS_TO_WRITE; ++i)  
        {  
            BOOST_LOG(lg) << "Some log record";  
        }  
  
        return 0;  
    }  
    catch (std::exception& e)  
    {  
        std::cout << "FAILURE: " << e.what() << std::endl;  
        return 1;  
    }  
}  
```  
  
The CMakeList.txt file is:  
  
```  
# CMakeList.txt : CMake project for Test, include source and define  
# project specific logic here.  
#  
cmake_minimum_required (VERSION 3.8)  
  
set (CMAKE_CXX_STANDARD 17)  
set (CMAKE_CXX_STANDARD_REQUIRED ON)  
set (CMAKE_CXX_EXTENSIONS OFF)  
  
project ("cpMain")  
  
#-------------------------------------------------------------------------------  
#  
# BOOST  
#  
#-------------------------------------------------------------------------------  
  
# we will use static libs  
set(Boost_USE_STATIC_LIBS ON)  # link statically  
#ADD_DEFINITIONS(-DBOOST_LOG_DYN_LINK)  # or, link dynamically  
  
# Boost::log required Boost version >= 1.76.0   
find_package(Boost 1.76 COMPONENTS log REQUIRED)  
  
include_directories(${Boost_INCLUDE_DIRS})  
link_directories(${Boost_LIBRARY_DIRS})  
  
#-------------------------------------------------------------------------------  
#  
# MAIN PROGRAM  
#  
#-------------------------------------------------------------------------------  
  
  
# Define Sources  
set(SRC_FILES  
    "main.cpp"  
	)  
  
#-----------------------  
#  
# Final Executable  
#  
#-----------------------  
  
# Add source to this project's executable.  
add_executable (cpMain  
						${SRC_FILES})  
						  
#-------------------------------------------------------------------------------  
#  
# TARGET LINK LIBRARIES  
#  
#-------------------------------------------------------------------------------  
  
# Link your application with OpenCV libraries  
target_link_libraries (cpMain  
	${Boost_LIBRARIES}  
	)  
```  
Thanks for the help,  
Luis

---

## Comment 1

> Username: Lastique  
> Created at: 2021-07-23 12:27:57 UTC  
> Updated at: 2021-07-23 12:28:49 UTC  
> Url: https://github.com/boostorg/log/issues/156#issuecomment-885603614  

I suspect, this is a duplicate of https://github.com/boostorg/filesystem/issues/172. Check whether your kernel headers on the build system match the kernel version you're running. Boost 1.77 will also add runtime detection of newer Linux syscalls.

---

## Comment 2

> Username: LFRS  
> Created at: 2021-07-26 14:35:17 UTC  
> Url: https://github.com/boostorg/log/issues/156#issuecomment-886758891  

That's it! Thanks @Lastique
