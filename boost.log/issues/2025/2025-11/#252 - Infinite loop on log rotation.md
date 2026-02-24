# #252 - Infinite loop on log rotation [Closed]

> Username: canon-cmre-antony-brown  
> Created at: 2025-11-05 13:14:41 UTC  
> Updated at: 2025-11-06 12:10:25 UTC  
> Closed at: 2025-11-05 22:08:43 UTC  
> Url: https://github.com/boostorg/log/issues/252  

Found in 1.86.0 (but we made a large move so I don't know where it came in).  
Observed also in 1.89.0.  
  
Minimal example, in boost 1.89.0  
```  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/sources/record_ostream.hpp>  
#include <boost/log/utility/setup/file.hpp>  
  
#include <filesystem>  
#include <iostream>  
  
int main()  
{  
    std::cout << "Test: Start" << std::endl;  
  
    const std::string filename = "infinite-loop-test.log";  
    std::error_code ec;  
    std::filesystem::remove(filename, ec);  
  
    auto sink = boost::log::add_file_log(  
        boost::log::keywords::file_name = filename,  
        boost::log::keywords::rotation_size = 512,  
        boost::log::keywords::open_mode = std::ios_base::app  
    );  
  
    boost::log::sources::logger log;  
  
    std::cout << "Test: Writing to log";  
    for (int i = 0; i < 100; ++i) {  
        std::string msg = "Error " + std::to_string(i);  
        BOOST_LOG(log) << msg;  
        std::cout << ".";  
    }  
    std::cout << std::endl;  
  
    std::cout << "Test: Finish" << std::endl;  
}  
```  
  
This example:  
* Uses logger (It was found using logger_mt)  
* deletes the log file at the start (so it stalls at a predictable point) but in our software append mode is used to add to an existing log file.  
* Has been build and tested with C++17 and C++20, using Visual Studio 2022 + vcpkg.  
  
I've attached a .zip with the solution etc: [boost-log-infinite-loop.zip](https://github.com/user-attachments/files/23360520/boost-log-infinite-loop.zip)  
  
When the log rotates, there is an infinite loop in text_file_backend.cpp:  
```  
1431: BOOST_LOG_API void text_file_backend::consume(record_view const& rec, string_type const& formatted_message  
1472:     while (!m_pImpl->m_File.is_open())  
1556:         continue;  
```  
If I increase the rotation_size or decrease the number of calls to log it does not stall.  
But If I massively increase both the rotation_size and the number of calls to log it still stalls.  
  
Our open mode, log location, file name, number of files and size are requirements of our customers, but I've tried a few things below for information:  
  
Prevents stall:  
* Removing the open_mode parameter  
* Using filename placeholders e.g. %N  
* Specifying a target that is not the required log directory (current dir) e.g. boost::log::keywords::target = "./rot"  
  
Does not prevent stall:  
* Using std::ios::out | std::ios::app open_mode  
* Specifying a target that is the required log directory (current dir) e.g. boost::log::keywords::target = "./" (or "./../boost-log-infinite-loop")  
* Setting max_files e.g. to 5  
  
I also found https://github.com/boostorg/log/issues/209 which may be related.

---

## Comment 1

> Username: Lastique  
> Created at: 2025-11-05 22:12:49 UTC  
> Url: https://github.com/boostorg/log/issues/252#issuecomment-3493772041  

Thanks for the report. Should be fixed now.  
  
I'll note that not including any placeholders (especially, the file counter placeholder) in the file name pattern and then requesting file rotation e.g. by setting `rotation_size` is not a useful setup. The sink backend will attempt to rotate the file but will obviously arrive to the same file, so the `rotation_size` limit will be exceeded, only with the overhead of continuous attempts to rotate the file.

---

## Comment 2

> Username: canon-cmre-antony-brown  
> Created at: 2025-11-06 12:10:24 UTC  
> Url: https://github.com/boostorg/log/issues/252#issuecomment-3496941475  

Thank you, that's brilliant. Given that it is not a useful setup I'll avoid specifying rotation_size when there is no rotation target - thank you for that also.
