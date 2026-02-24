# #209 - An infinite loop occurs when boost writes to the log file [Closed]

> Username: alvin906  
> Created at: 2023-04-19 08:13:23 UTC  
> Updated at: 2023-05-10 17:27:45 UTC  
> Closed at: 2023-05-10 17:21:13 UTC  
> Url: https://github.com/boostorg/log/issues/209  

* boost version: 1.81.0   
* FileSink keywords::rotation_size = (200<<20)  
* log file size now: 209,945,411 byte  
![Snipaste_2023-04-19_16-02-18](https://user-images.githubusercontent.com/13476563/233009183-5cba87b6-935f-429f-968a-bdf88c0d2b06.png)  
* formartted message size: 226 byte  
![Snipaste_2023-04-19_16-03-33](https://user-images.githubusercontent.com/13476563/233009487-930af51b-223b-4e42-bd9d-1baf1edadbd4.png)  
* FileRotationSize:  209,945,411 byte  
![Snipaste_2023-04-19_16-03-57](https://user-images.githubusercontent.com/13476563/233009589-ad717173-0b78-4dd8-b9fd-2443ed17a482.png)  
  
#### debugging:  
![boostlog](https://user-images.githubusercontent.com/13476563/233011632-5fcf2a2a-2dda-48ec-85d4-da630e34fdc1.gif)  
  
I write the log to the file in this way:   
```  
         // global define  
          BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(boost_logger, src::logger_mt)  
           ...  
           // Append log to log file  
            BOOST_LOG(boost_logger::get()) << strHeader << strBody;  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-19 15:15:37 UTC  
> Url: https://github.com/boostorg/log/issues/209#issuecomment-1514918332  

Please, provide a minimal compilable code sample that reproduces the problem.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-04-19 15:26:52 UTC  
> Url: https://github.com/boostorg/log/issues/209#issuecomment-1514935858  

From your debugging gif, it looks like you have lots of log files in the logging directory, and the backend iterates over them trying to find the log file name that is not yet used. This isn't an infinite loop, unless you have 4 billion log files in the directory.

---

## Comment 3

> Username: christapley  
> Created at: 2023-05-10 07:46:36 UTC  
> Updated at: 2023-05-10 07:47:24 UTC  
> Url: https://github.com/boostorg/log/issues/209#issuecomment-1541512155  

I've ran into the same problem. It was introduced by #179  
  
```  
#include <vector>  
  
#include <boost/log/core.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/trivial.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/utility/setup/file.hpp>  
  
namespace logging = boost::log;  
namespace keywords = boost::log::keywords;  
  
void init_logging()  
{  
    logging::register_simple_formatter_factory<logging::trivial::severity_level, char>("Severity");  
  
    auto file = logging::add_file_log(  
        keywords::file_name        = "sample_%N.log",    //  
        keywords::target           = "./",               //  
        keywords::target_file_name = "sample_%N.log",    //  
        keywords::open_mode        = std::ios_base::app, //  
        keywords::auto_flush       = true,               //  
        keywords::rotation_size    = 500*1024,           //  
        keywords::max_size         = 6*1024*1024,        //  
        keywords::max_files        = 6,  
        keywords::format =  
            "%LineID%. [%TimeStamp%][%ThreadID%] [%Severity%] - %Message%");  
  
    file->locked_backend()->scan_for_files();  
  
    logging::core::get()->set_filter(logging::trivial::severity >= logging::trivial::info);  
  
    logging::add_common_attributes();  
}  
  
int main()  
{  
    init_logging();  
  
    std::vector<char> value(static_cast<size_t>(1024*1024), 'a');  
    value.push_back('\0');  
  
    BOOST_LOG_TRIVIAL(info) << value.data();  
    BOOST_LOG_TRIVIAL(info) << value.data();  
    BOOST_LOG_TRIVIAL(info) << value.data();  
    BOOST_LOG_TRIVIAL(info) << value.data();  
  
}  
```  
If the log line exceeds the rotation size then we'll be stuck in an infinite loop.  
  
This is a possible resolution  
```  
diff --git a/src/text_file_backend.cpp b/src/text_file_backend.cpp  
index 8b8920e..8b0b6a7 100644  
--- a/src/text_file_backend.cpp  
+++ b/src/text_file_backend.cpp  
@@ -1480,8 +1480,10 @@ BOOST_LOG_API void text_file_backend::consume(record_view const& rec, string_typ  
         m_pImpl->m_IsFirstFile = false;  
   
         // Check the file size before invoking the open handler, as it may write more data to the file  
+        // But don't rotate the file if the current log message itself exceeds the rotate file size  
         m_pImpl->m_CharactersWritten = static_cast< std::streamoff >(m_pImpl->m_File.tellp());  
-        if (m_pImpl->m_CharactersWritten + formatted_message.size() >= m_pImpl->m_FileRotationSize)  
+        if (m_pImpl->m_CharactersWritten + formatted_message.size() >= m_pImpl->m_FileRotationSize  
+            && formatted_message.size() < m_pImpl->m_FileRotationSize)  
         {  
             // Avoid running the close handler, as we haven't run the open handler yet  
             struct close_handler_backup_guard  
```  
  
Any hints on what sort of solution boost would accept @Lastique ?

---

## Comment 4

> Username: Lastique  
> Created at: 2023-05-10 17:27:45 UTC  
> Url: https://github.com/boostorg/log/issues/209#issuecomment-1542563443  

@christapley Thank you for the reproducer, although I'm not sure your case is the same as the OP's. In the original problem description, the log message size is definitely smaller than the `rotation_size` limit, and it looks like every time a log file is open on his system, it is already too large to fit a new record.  
  
In any case, I have committed a fix for your reproducer in b5c42771403a272800bd423a763366bbe70054c1 and also an additional safeguard in 407260f24f136d8948b1ae1726fb57499672c805. I'm not sure it'll fix the OP's problem, but I haven't heard from him, so I'm closing this report.
