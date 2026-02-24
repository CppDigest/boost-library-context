# #125 - scan_for_files skips file count [Closed]

> Username: ghost  
> Created at: 2020-08-20 14:14:10 UTC  
> Updated at: 2020-08-20 17:01:21 UTC  
> Closed at: 2020-08-20 17:00:57 UTC  
> Url: https://github.com/boostorg/log/issues/125  

I set up a text_file_backend to rotate log files with a counter appended to the target file name  
```cpp  
auto backend = boost::make_shared<sinks::text_file_backend>(  
  keywords::file_name = "foo.log",  
  keywords::target_file_name = "foo.log.%N",  
  keywords::rotation_size = 10,	// small size for testing  
  keywords::open_mode = std::ios_base::out | std::ios_base::app  
);  
using file_sink = sinks::asynchronous_sink<sinks::text_file_backend>;  
auto sink = boost::make_shared<file_sink>(backend);  
  
sink->locked_backend()->set_file_collector(  
  boost::log::sinks::file::make_collector(  
    keywords::target = log_dir,  
    keywords::max_files = 10  
  )  
);  
  
sink->locked_backend()->scan_for_files();  
logging::core::get()->add_sink(sink);  
```  
If I set up a program to log 3 entries during one run of the program.  After two runs, the logs look like:  
```bash  
$ ls foo.log*  
foo.log  foo.log.1  foo.log.2  foo.log.4  foo.log.5  
```  
The rotation works except `foo.log.3` was skipped.  If instead I log 2 entries (the second one to cause a file rotation) during one run of the program, and run it 6 times I get:  
```bash  
$ ls foo.log*  
foo.log    foo.log.11  foo.log.5  foo.log.9  
foo.log.1  foo.log.3   foo.log.7  
```  
We get only odd numbers because one count was skipped at each start of the program (at scan_for_files)  
  
I tried making the `rotation_size` bigger, putting the counter elsewhere (e.g. in the middle of the file name), not opening the file in append mode, but to no avail.  
  
I am using Boost 1.73

---

## Comment 1

> Username: Lastique  
> Created at: 2020-08-20 17:01:21 UTC  
> Url: https://github.com/boostorg/log/issues/125#issuecomment-677785594  

Thanks for the report, should be fixed now in develop.
