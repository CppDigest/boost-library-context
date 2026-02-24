# #77 - Rotating log files -- the wrong files are being deleted [Closed]

> Username: adaviding  
> Created at: 2019-03-08 19:48:07 UTC  
> Updated at: 2019-03-08 23:04:03 UTC  
> Closed at: 2019-03-08 23:04:03 UTC  
> Url: https://github.com/boostorg/log/issues/77  

I am using boost 1.66.0.  I am using the rotating logger from my c++ application (which runs on windows).  
  
I have configured a rotating logger to keep no more than 50 log files.  I assumed it would delete the oldest log files in the folder to make room for the new ones, but that is not what happens.  
  
When my folder contains around 49 log files and my application is started, it begins to write the 50th file as expected.  After a while, there are enough logs to roll into new files.  When I come back and look at the log files, I discover that the first 49 log files are still there.  (These are the files that existed before my application was started.)  
  
Also I will see 1 or 2 of the most recent log files, but I am missing the log files that were written when the application was started.  Presumably the limit of 50 (or 51) files is being enforced, but the wrong files are being deleted.  It is deleting the oldest files that were written after the application was started; it is not deleting log files that existed before the application was started.  
  
For your reference, here is the code where I configure my logger.  
  
```  
// maximum number of stored files  
#define MAX_LOG_FILES 50  
  
// maximum file size of rotated file.  
#define MAX_LOG_FILE_SIZE 16*1024*1024  
  
// maximum total size of the stored files, in bytes  
#define MAX_LOG_BYTES_TOTAL MAX_LOG_FILES*1024*1024  
  
// minimum free space on the drive, in bytes  
#define MIN_LOG_DRIVE_SPACE 4*MAX_LOG_BYTES_TOTAL  
  
  
PanErr __cdecl Logger::initialize(  
    _In_ const boost::filesystem::path& logFolder,  
    _In_ const LogLevel consoleThreshold,  
    _In_ const LogLevel fileThreshold)  
{  
    using namespace boost::log;  
  
    PanErr err = PanErr::ok;  
  
    std::unique_lock<std::mutex> lock(m_initializationMutex);  
  
    //  Undo any prior initialization  
    this->dispose();  
  
    //  Hold a pointer to the core in order to prevent possible problems with order of destruction.  
    m_boostLoggingCore = boost::log::core::get();  
  
    // Store the log folder.  
    m_logFolder = logFolder;  
  
    // Compute the log-level threshold across both sinks.  
    m_threshold = (consoleThreshold < fileThreshold) ? consoleThreshold : fileThreshold;  
  
    // Construct the console sink.  
    m_consoleSink = boost::shared_ptr<BoostConsoleSink>(new BoostConsoleSink);  
  
    try  
    {  
        // Pipe to std::clog.  
        m_consoleSink->locked_backend()->add_stream(  
            boost::shared_ptr<std::ostream>(&std::clog, boost::null_deleter()));  
  
        // The format of each log line is like this.  
        m_consoleSink->set_formatter  
        (  
            expressions::format("%1% %2% %3% %4%:%5% %6%")  
            % expressions::format_date_time<boost::posix_time::ptime>(  
                TimestampKeyword.get_name(), "%y-%m-%dT%H:%M:%S.%f")  
            % LevelKeyword  
            % CategoryKeyword  
            % FileKeyword  
            % LineKeyword  
            % MessageKeyword  
        );  
  
        // Set the console threshold.  
        m_consoleSink->set_filter(expressions::attr<LogLevel>(LevelKeyword.get_name()) >= consoleThreshold);  
  
        // Construct the file sink.  
        m_fileSink = boost::shared_ptr<BoostFileSink>(new BoostFileSink(  
            keywords::file_name = (logFolder / "%Y-%m-%d_%H-%M-%S_%5N.log").string(),  // file name pattern  
            keywords::rotation_size = MAX_LOG_FILE_SIZE                                // rotation size, in characters  
        ));  
  
        // Set up where the rotated files will be stored.  
        m_fileSink->locked_backend()->set_file_collector(sinks::file::make_collector(  
            keywords::target = logFolder,                       // where to store rotated files (doesn't actually work)  
            keywords::max_size = MAX_LOG_BYTES_TOTAL,           // maximum total size of the stored files, in bytes  
            keywords::min_free_space = MIN_LOG_DRIVE_SPACE,     // minimum free space on the drive, in bytes  
            keywords::max_files = MAX_LOG_FILES                 // maximum number of stored files  
        ));  
          
        // In the boost examples, keywords::target (above) is supposed to designate the log folder but it doesn't  
        // seem to work.  The only thing that worked was to set keywords::file_name (above) to an absolute folder path.  
  
        // Upon restart, scan the target directory for files matching the file_name pattern.  
        m_fileSink->locked_backend()->scan_for_files();  
  
        // The format of each log line is like this.  
        m_fileSink->set_formatter  
        (  
            expressions::format("%1% %2% %3% %4%:%5% %6%")  
            % expressions::format_date_time<boost::posix_time::ptime>(  
                TimestampKeyword.get_name(), "%Y-%m-%d %H:%M:%S.%f")  
            % LevelKeyword  
            % CategoryKeyword  
            % FileKeyword  
            % LineKeyword  
            % MessageKeyword  
        );  
  
        // Set the threshold.  
        m_fileSink->set_filter(expressions::attr<LogLevel>(LevelKeyword.get_name()) >= fileThreshold);  
  
        // Add sinks to the core.  
        m_boostLoggingCore->add_sink(m_consoleSink);  
        m_boostLoggingCore->add_sink(m_fileSink);  
  
        // Indicate that the logger has been initialized.  
        m_initialized = true;  
    }  
    catch (std::exception& ex)  
    {  
        err = PanErr::fail;  
        std::cout <<  "ERROR  Failed to initialize logging system:" << std::endl << ex.what() << std::endl;  
    }  
  
Cleanup:  
    return err;  
}  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2019-03-08 21:11:07 UTC  
> Url: https://github.com/boostorg/log/issues/77#issuecomment-471077480  

The files are removed from the oldest ones to the newest, ordered by the last modify time of the file. The files that are not found during scanning are not tracked (and therefore not removed). So either there is something wrong with modify times in your filesystem, or the scanning didn't pick up the files from the previous runs of your application. Note that file deletion may happen not only due to file number limit.  
  
Judging by the comments in your code, you seem to have a problem with the file collector. This may be related to the problem since it is the file collector that manages the target directory and does file deletion.  
  
Also, 1.66 is a rather old version, please try to reproduce with the latest release of Boost or develop branch.

---

## Comment 2

> Username: adaviding  
> Created at: 2019-03-08 23:04:03 UTC  
> Url: https://github.com/boostorg/log/issues/77#issuecomment-471106796  

I upgraded to 1.69.0 and it is working ... thanks for the suggestion and I'm sorry to have bothered you.
