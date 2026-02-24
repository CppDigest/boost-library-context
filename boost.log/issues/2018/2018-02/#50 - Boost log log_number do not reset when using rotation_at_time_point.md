# #50 - Boost log log_number do not reset when using rotation_at_time_point [Closed]

> Username: hoangtocdo90  
> Created at: 2018-02-13 07:42:19 UTC  
> Updated at: 2018-02-13 09:13:18 UTC  
> Closed at: 2018-02-13 09:13:18 UTC  
> Url: https://github.com/boostorg/log/issues/50  

Hi guys i'm using boost log for rotation by day and file size, i also set max log size store in logdir  
my log file are :   
log_2018-02-20_000.log  
log_2018-02-20_001.log  
log_2018-02-20_002.log  
log_2018-02-21_003.log  
log_2018-02-21_004.log  
  
How to reset log file number to zero when new day come ? of course i'm still need  log max_size limit  
something like :   
log_2018-02-20_000.log  
log_2018-02-20_001.log  
log_2018-02-20_002.log  
log_2018-02-21_000.log  
log_2018-02-21_001.log  
  
Here is my code  
  
`               log_file_path = LOGS_PATH + L"/log_%Y-%m-%d_%3N.log";  
  
		sink = boost::log::add_file_log  
		(  
			keywords::file_name = log_file_path,  
			keywords::rotation_size = LOG_ROTATION_SIZE * 1024 * 1024,  
			keywords::time_based_rotation = sinks::file::rotation_at_time_point(0, 0, 0), /*< ...or at midnight >*/  
			keywords::format = log_format,  
			keywords::filter = boost::log::trivial::severity >= level && (expr::has_attr(a_channel) && a_channel == LOG_NAME),  
			//keywords::open_mode = std::ios::app,  
			//keywords::scan_method = boost::log::sinks::file::scan_matching,  
			keywords::auto_flush = true,  
			keywords::target = LOGS_PATH,  
			keywords::channel = LOG_NAME  
  
  
  
		);  
  
        std::locale loc = boost::locale::generator()("ja_JP.UTF-8");  
	sink->imbue(loc);  
	boost::log::add_common_attributes();  
	if (LOG_MAX_SIZE > 0) {  
  
		sink->locked_backend()->set_file_collector(sinks::file::make_collector(  
			keywords::target = LOGS_PATH,  
			keywords::max_size = LOG_MAX_SIZE * 1024 * 1024  
			//,keywords::min_free_space = 100 * 1024 * 1024,  
			//keywords::max_files = 512  
		));  
	}  
`

---

## Comment 1

> Username: Lastique  
> Created at: 2018-02-13 09:13:18 UTC  
> Url: https://github.com/boostorg/log/issues/50#issuecomment-365197833  

There is no way to reset the file counter other than to recreate the sink. See also https://github.com/boostorg/log/issues/49.
