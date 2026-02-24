# #89 - Boost.Log How to config current logging path [Closed]

> Username: armleung  
> Created at: 2019-08-09 07:50:13 UTC  
> Updated at: 2019-08-25 15:21:21 UTC  
> Closed at: 2019-08-25 15:21:21 UTC  
> Url: https://github.com/boostorg/log/issues/89  

I have a application installed at ProgramFile folder , and the file collector target path be ProgramData. It is fine when the OS is under winXP.   
  
However, after migrated to Win10, my program cannot write any log because of privileges problem.   
How can i define all the logging file location be the "ProgramData" instead of "current path" ?   
  
```  
// add file sink  
auto file_sink = boost::make_shared< sinks::synchronous_sink<sinks::text_file_backend> >(  
keywords::file_name = "Logging_Op%N.log",   
keywords::rotation_size = 5 * 1024 * 1024,  
keywords::open_mode = std::ios_base::app | std::ios_base::out,  
keywords::filter = channel == "general" );  
  
file_sink->locked_backend()->set_file_collector(sinks::file::make_collector(  
keywords::target = "C:\\ProgramData\\MyApplication\\", //   
keywords::max_size = 2 * 10* 5 * 1024 * 1024));  
```

---

## Comment 1

> Username: mozeat-sun  
> Created at: 2019-08-09 07:59:35 UTC  
> Url: https://github.com/boostorg/log/issues/89#issuecomment-519821727  

why not use settings.ini ?

---

## Comment 2

> Username: mozeat-sun  
> Created at: 2019-08-09 08:03:17 UTC  
> Url: https://github.com/boostorg/log/issues/89#issuecomment-519822809  

#  
#          Copyright Andrey Semashev 2007 - 2014.  
# Distributed under the Boost Software License, Version 1.0.  
#    (See accompanying file LICENSE_1_0.txt or copy at  
#          http://www.boost.org/LICENSE_1_0.txt)  
#  
  
[Core]  
  
Filter="%Severity% >= 2"  
  
  
[Sinks.1]  
  
Destination=Console  
Format="%TimeStamp% *** %Message%"  
Filter="%Tag% | %Severity% > 3"  
  
  
[Sinks.2]  
  
Destination=TextFile  
FileName=test.log  
Target=/var/log/ZOO  
AutoFlush=true  
Append=true  
RotationSize=2097152  
Format="[%TimeStamp%] [%Tag%] %Message%"

---

## Comment 3

> Username: Lastique  
> Created at: 2019-08-25 15:21:21 UTC  
> Url: https://github.com/boostorg/log/issues/89#issuecomment-524638478  

> How can i define all the logging file location be the "ProgramData" instead of "current path" ?  
  
You can include the path in the `file_name` named parameter.
