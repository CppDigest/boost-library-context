# #218 - How to append log to same log file after program restarts? [Closed]

> Username: sinall  
> Created at: 2023-09-20 02:40:39 UTC  
> Updated at: 2023-09-20 08:55:26 UTC  
> Closed at: 2023-09-20 08:55:25 UTC  
> Url: https://github.com/boostorg/log/issues/218  

We are trying to use Boost.Log in a command line tool. User starts the cmd tool, and it will exit after it's done. We want to be able to record all logs for each execution of this tool to same log file. We don't want to create new log file each time because it will be too many files.  
  
We are using settings file as below：  
  
```  
[Core]  
Filter="%Severity% >= debug"  
  
[Sinks.Console]  
Destination=Console  
Format="[%TimeStamp%] [%Severity%] [%ProcessID%] [%ThreadID%] %Message%"  
Filter="%Tag% | %Severity% > info"  
  
[Sinks.runc]  
Destination=TextFile  
Target="/storage/app/data/log"  
FileName="/storage/app/data/log/runc.log"  
AutoFlush=true  
Format="[%TimeStamp%] [%Severity%] [%ProcessID%] [%ThreadID%] %Message%"  
OpenMode=append  
```  
  
Code as below:  
```  
std::ifstream confStream("/etc/logging.conf");  
if (!confStream.is_open()) {  
    std::cout << "Could not open logging.conf file " << strerror(errno) << std::endl;  
    return;  
}  
  
auto settings = logging::parse_settings(confStream);  
logging::init_from_settings(settings);  
  
logging::add_common_attributes();  
```  
  
As you can see `OpenMode` is set to `append`. However, when the program is restarted, the log cannot be appended to the previous log file, but directly overwrites the content of the previous log file. Did I misunderstand the meaning of the OpenMode parameter? If I want to append the new logs to the previous log file when restarting the program, what should I do?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-09-20 08:55:25 UTC  
> Url: https://github.com/boostorg/log/issues/218#issuecomment-1727274413  

There is no `OpenMode` parameter. There is `Append` parameter which takes a value of `true` or `false`. See parameter description [here](https://www.boost.org/doc/libs/1_83_0/libs/log/doc/html/log/detailed/utilities.html#log.detailed.utilities.setup.settings).
