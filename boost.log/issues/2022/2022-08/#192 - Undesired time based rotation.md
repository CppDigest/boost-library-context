# #192 - Undesired time based rotation [Closed]

> Username: andreabonacini  
> Created at: 2022-08-08 10:32:59 UTC  
> Updated at: 2022-08-09 10:16:40 UTC  
> Closed at: 2022-08-09 10:16:40 UTC  
> Url: https://github.com/boostorg/log/issues/192  

Hi,  
  
I'm using Boost Log version 1.72.0 and i'm having a strange behavior in my application where i want 3 file sinks for 3 different log files with this folder structure:  
  
> Logs/  
|____ File/  
|__________ File1_%Y%m%d_%H_%M_%S.log (produced by sinkFile1)  
|__________ ...  
|__________ File2_%Y%m%d_%H_%M_%S.log (produced by sinkFile2)  
|__________ ...  
|  
|____ App_%Y%m%d_%H_%M_%S.log (produced by sinkApp)  
|____ ...  
  
The problem is:   
the logging works well when the application run at day 1, 2 and 3, but when it is launched at day 4 the files named App_etc.log logged at day 1 are deleted. This happen even if no dimension limit (file or dir) is reached for each sink. Instead, if i delete the File folder and disable (with a flag in the code) the logging to it, the logging of App_etc.log keep going on without deleting 2-days older files.  
This behavior in unusual beacause i haven't set a time based rotation in any sinks, but it still seems to happen.   
  
To setup that logging structure i call in my code the function add_file_log as following:  
  
```  
const std::string RECORD_FORMAT = "[%TimeStamp%] %ThreadID% %ModuleID% [%Severity%]\t: %Message%";  
const std::string APP_NAME = "App_%Y%m%d_%H_%M_%S.log";  
const std::string FILE1_NAME = "File1_%Y%m%d_%H_%M_%S.log";  
const std::string FILE2_NAME = "File2_%Y%m%d_%H_%M_%S.log";  
  
sinkApp = add_file_log(keywords::format = RECORD_FORMAT,                             
	                  keywords::target = "Logs",                            
			  keywords::file_name = "Logs\\" + APP_NAME ,  
			  keywords::rotation_size  = 2 * 1024 * 1024,             
			  keywords::max_size = 1000 * 1024 * 1024,                               
			  keywords::min_free_space = 25 * 1024Ui64 * 1024Ui64,        
			  keywords::auto_flush = true);  
  
sinkFile1 = add_file_log(keywords::format = RECORD_FORMAT,                             
			   keywords::target = "Logs\\File",                          
			   keywords::file_name = "Logs\\File\\" + FILE1_NAME,   
			   keywords::rotation_size = 1000 * 1024 * 1024,                
			   keywords::max_size = 1000 * 1024 * 1024,                
			   keywords::auto_flush = true);  
  
sinkFile2 = add_file_log(keywords::format = RECORD_FORMAT,                             
			   keywords::target = "Logs\\File",                          
			   keywords::file_name = "Logs\\File\\" + FILE2_NAME,  
			   keywords::rotation_size = 1000 * 1024 * 1024,                
			   keywords::max_size = 1000 * 1024 * 1024,                
			   keywords::auto_flush = true);  
  
```  
  
Do you have any suggestions about this issue?  
Maybe something about scan_for_files?  
  
Thanks in advance

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-08 11:02:16 UTC  
> Url: https://github.com/boostorg/log/issues/192#issuecomment-1207977246  

You are enabling rotation by specifying `target` argument and setting `rotation_size` limit. Further, you are enabling removal of older log files by setting `max_size` and `min_free_space` limits. Note that there is only one file collector created per target directory, and it has only one set of limits it applies to that directory - the limits that were first specified when that file collector was created (i.e. when the first `add_file_log` call was made for that `target`). This also means that the collector does not distinguish files produced by different sinks - as long as they are stored in the same target directory, they will be treated without discretion (e.g. this may lead to preserving less files from one sink than from the other). So, I suspect what you are seeing is the expected cleanup of the target directories.

---

## Comment 2

> Username: andreabonacini  
> Created at: 2022-08-08 12:32:16 UTC  
> Url: https://github.com/boostorg/log/issues/192#issuecomment-1208067955  

I know that only one collector is associated to each folder and so that in my case the first `add_file_log` set the collector for the "Logs" folder while the second `add_file_log` set the collector for the "Logs\\File" folder (the third one is redundant due the second one).   
  
FirstCollector:   
- target = "Log"  
- max_size = 1000 MiB  
- min_free_space = 25 MiB  
  
SecondCollector:  
- target = "Log\\File"  
- max_size = 1000 MiB  
  
During the executions i mentioned, `max_size` and `min_free_space` limits were never been reached because files are really small and the disk is pretty empty.  
At day 4 (e.g. 20220807 in the following example) just before the application starts i have a situation like:  
  
> Logs/  
|____ File/  
|__________ File1_20220804_etc.log [40 MB]  
|__________ File2_20220804_etc.log [44 MB]   
|  
|____ App_20220806_etc.log [1 MB]  
|____ App_20220806_etc.log [1 MB]  
|____ App_20220806_etc.log [1 MB]  
|____ App_20220805_etc.log [1 MB]  
|____ App_20220805_etc.log [1 MB]  
|____ App_20220804_etc.log [1 MB]  
|____ App_20220804_etc.log [1 MB]  
|____ App_20220804_etc.log [1 MB]  
|____ App_20220803_etc.log [1 MB] --> deleted  
|____ App_20220803_etc.log [1 MB] --> deleted  
|____ App_20220803_etc.log [1 MB] --> deleted  
  
All App_etc.log with 20220803 timestamp are deleted as if a time rotation had been set (since dimension limits are not reached). Instead, if i delete del File folder before run the application, those files stay in the folder.  
  
So i think it is not a problem correlated to the reason you are talking about. Is there a default time rotation or something similar?   
  
Or maybe some conflict between nested collectors? Because with a logging structure like "Logs/File/File_etc.log" and "Logs/App/App_etc.log" i have no problems.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-08-08 13:55:06 UTC  
> Url: https://github.com/boostorg/log/issues/192#issuecomment-1208163198  

> Is there a default time rotation or something similar?  
  
No.  
  
> Or maybe some conflict between nested collectors?  
  
Not that I can see. `scan_for_files` only collects information of regular files (i.e. not directories) and does not recurse into directories. So different collectors should not touch each other's files.
