# #110 - Logging to non-rotating file broken [Closed]

> Username: d1chang  
> Created at: 2020-03-29 22:28:41 UTC  
> Updated at: 2020-03-29 22:49:45 UTC  
> Closed at: 2020-03-29 22:49:44 UTC  
> Url: https://github.com/boostorg/log/issues/110  

This simple example is currently broken in Boost version 1.72.0 (it worked in 1.69.0):   
```  
void init()  
{  
    log::add_file_log("sample.log");  
  
    log::core::get()->set_filter  
    (  
        log::trivial::severity >= logging::trivial::info  
    );  
}  
```  
(from https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/tutorial/sinks.html )   
  
Observed result: log is written to 00000.log   
Expected result: log is written to sample.log   
  
The current workaround is to specify the following:   
```  
    log::add_file_log(log::keywords::file_name = "sample.log", log::keywords::target_file_name = "sample.log");  
```  
Note that it is insufficient to specify only file_name or target_file_name; both must be specified.   
  
Proposed fix: I propose fixing by also setting keywords::target_file_name to file_name_arg (in addition to existing setting of keywords::file_name to file_name_arg) in the various calls to aux::add_file_log in file.hpp.   
  
Environment: Boost version 1.72.0, x32, MSVC 19.16.27038 for x86 (also observed in 19.25.28610.4 for x86)

---

## Comment 1

> Username: Lastique  
> Created at: 2020-03-29 22:49:44 UTC  
> Url: https://github.com/boostorg/log/issues/110#issuecomment-605714336  

Duplicates https://github.com/boostorg/log/issues/104.
