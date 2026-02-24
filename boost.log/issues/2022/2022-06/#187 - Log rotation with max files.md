# #187 - Log rotation with max files [Closed]

> Username: andreabonacini  
> Created at: 2022-06-24 13:24:11 UTC  
> Updated at: 2022-07-01 22:11:37 UTC  
> Closed at: 2022-07-01 22:11:37 UTC  
> Url: https://github.com/boostorg/log/issues/187  

Hi,  
  
I'm using Boost Log version 1.72.0 and i'm trying to  handle the rotation with max_files too. I initialize the logging system with this function:  
  
```  
bool initLogging(bool consoleLog, bool fileLog, std::string ThreadID) {  
	using namespace boost::log;  
	try {  
		if (!consoleLog && !fileLog) {  
			core::get()->set_logging_enabled(false);  
			return false;  
		}  
		core::get()->remove_all_sinks();  
		add_common_attributes();  
		core::get()->add_thread_attribute("ThreadID", Log::attr(ThreadID));    
		if (consoleLog) add_console_log(std::cout, keywords::format = "[%TimeStamp%] %ThreadID%\t[%Severity%]  \t: %Message%");   
		if (fileLog) add_file_log(keywords::file_name      = "Log/App_%Y%m%d_%H_%M_%S.log",                                                   
								  keywords::target         = "Log",                           
								  keywords::rotation_size  = 1 * 1024 * 1024,                                                                 
								  keywords::format         = "[%TimeStamp%] %ThreadID%\t[%Severity%]\t: %Message%",  
								  keywords::max_files      = 5,                                                                               
								  keywords::max_size       = 5 * 1024 * 1024,                                                               
								  keywords::min_free_space = 10Ui64 * 1024Ui64 * 1024Ui64 * 1024Ui64,                                          
								  keywords::auto_flush     = true);                                                                                
	} catch (...) {  
		return false;  
	}  
	return true;  
}  
```  
  
Since i need to start with just a console log and then, depending on a config file, set console and/or file log, i call this function 2 times like (in this way, if something goes wrong during setup i have at least console log):  
  
```  
initLogging(true, false, "_MASTER_");  
// read config file...  
initLogging(consoleConfig, fileConfig, "_MAST_");  
// Start logging...  
```  
  
Problem: the max_files = 5 seems ignored because multiple run of the app does not delete older files to keep that number but still increase. Am i producing some kind of conflict with these calls?  
  
Bonus question: the second call can not change the attribute ThreadID from "_MASTER_" to "_MAST_". Should i need to remove the attribute and then re-add it or is there a simple way for change it?  
  
Thanks in advance

---

## Comment 1

> Username: Lastique  
> Created at: 2022-06-24 16:21:54 UTC  
> Url: https://github.com/boostorg/log/issues/187#issuecomment-1165731484  

I don't immediately see the problem wrt. `max_files`. However, you should know that if there are multiple sinks targeting the same storage directory, they will use the same file collector, and it will use storage limits that were specified first. If you're keeping only one sink per target directory, this should not concern you. You may want to run your code in debugger to see if the previously stored files are found during `file_collector::scan_for_files`.  
  
Re. attributes, `core::add_thread_attribute` acts similarly to `std::map::insert` - it will add the attribute if it was not added before. Otherwise it will return an iterator to the previously added attribute and a `false`. If you want to replace the attribute, you should either remove it first or update its value through the returned iterator (if the attribute supports this). The `add_common_attributes()` call is calling `core::add_global_attribute` internally, which works the same way. `add_common_attributes` doesn't update the attributes if they are already registered, so the second call has no effect (unless you have removed the attributes) and is redundant.

---

## Comment 2

> Username: andreabonacini  
> Created at: 2022-07-01 08:28:35 UTC  
> Url: https://github.com/boostorg/log/issues/187#issuecomment-1172083940  

Thanks for the quick answer. I found that the first problem was happening because i was suddenly killing the application to test the rotation but seems that the file removal take place only when the a rotation occours or at the end of the process, fair enough.  
  
Anyway i'm trying to update at runtime the target directory for a text file sink. It has been initialized with a certain target (ie "Log/") and after some application cycles i would like to change the target for that sink (ie "Log2/" or "Log/old/").  
  
I'm trying to do this through the sink pointer:   
  
`filePtr->locked_backend()->set_target_file_name_pattern<std::string>(module_dirPath)`  
  
but the application throw an exception when has to rotate, any suggestions?   
  
Thanks

---

## Comment 3

> Username: Lastique  
> Created at: 2022-07-01 12:38:06 UTC  
> Url: https://github.com/boostorg/log/issues/187#issuecomment-1172302234  

If you want to change the target directory, you need to create a new file collector (`sinks::file::make_collector`) for the new directory and set it to the sink backend (`text_file_backend::set_file_collector`). Note that you have to specify all target storage limits to the new collector.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-07-01 12:45:16 UTC  
> Url: https://github.com/boostorg/log/issues/187#issuecomment-1172308177  

Also, if your new directory contains files that you want to track, you will also want to call `text_file_backend::scan_for_files`).  
  
If you want to use `add_file_log`, you need to remove the old sink added by the first call and then call `add_file_log` with the new target. But in this case you may lose some log records that are made while there are no file sinks registered in the core.

---

## Comment 5

> Username: Lastique  
> Created at: 2022-07-01 22:11:37 UTC  
> Url: https://github.com/boostorg/log/issues/187#issuecomment-1172753037  

In any case, I believe the original problem is solved on your end.
