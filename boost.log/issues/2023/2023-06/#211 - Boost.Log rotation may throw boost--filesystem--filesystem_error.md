# #211 - Boost.Log rotation may throw boost::filesystem::filesystem_error [Closed]

> Username: debuggingbug  
> Created at: 2023-06-09 09:27:01 UTC  
> Updated at: 2023-06-12 05:29:40 UTC  
> Closed at: 2023-06-09 11:26:18 UTC  
> Url: https://github.com/boostorg/log/issues/211  

Only throw on a few machine, but I think it's worth reporting.  
  
Unhandled exception at 0x00007FF8A2CECD29 in 20230710175425258.dmp: Microsoft C++ exception: boost::filesystem::filesystem_error at memory location 0x000000A617BFF6E8.  
  
```  
 	KERNELBASE.dll!RaiseException()	Unknown	Non-user code. Symbols loaded without source information.  
>	VCRUNTIME140_1.dll!__FrameHandler4::CxxCallCatchBlock(_EXCEPTION_RECORD * pExcept) Line 1459	C++	Symbols loaded.  
 	ntdll.dll!RcConsolidateFrames()	Unknown	Symbols loaded.  
 	hoyo_desk.exe!boost::log::v2_mt_nt6::sinks::basic_formatting_sink_frontend<char>::feed_record<boost::recursive_mutex,boost::log::v2_mt_nt6::sinks::text_file_backend>(const boost::log::v2_mt_nt6::record_view & rec, boost::recursive_mutex & backend_mutex, boost::log::v2_mt_nt6::sinks::text_file_backend & backend) Line 465	C++	Symbols loaded.  
 	hoyo_desk.exe!boost::log::v2_mt_nt6::sinks::asynchronous_sink<boost::log::v2_mt_nt6::sinks::text_file_backend,boost::log::v2_mt_nt6::sinks::unbounded_fifo_queue>::run() Line 398	C++	Symbols loaded.  
 	boost_thread-vc143-mt-x64-1_82.dll!00007ff87c9890ca()	Unknown	Non-user code. Binary was not built with debug information.  
 	ucrtbase.dll!thread_start<unsigned int (__cdecl*)(void *),1>()	Unknown	Non-user code. Symbols loaded without source information.  
 	kernel32.dll!BaseThreadInitThunk()	Unknown	Non-user code. Symbols loaded without source information.  
 	ntdll.dll!RtlUserThreadStart()	Unknown	Non-user code. Symbols loaded without source information.  
  
```  
  
The codes:  
```cpp  
void configLoggers(const hoyodesk::server::log::Config& config) noexcept {  
    namespace fs = std::filesystem;  
    auto core = logging::core::get();  
    if (!config.directory.empty()) {  
        if (config.is_file_backend_enabled) {  
            typedef sinks::asynchronous_sink<sinks::text_file_backend> FileSink;  
  
            fs::path log_file{config.directory / (config.stem + L".log")};  
            auto file_sink = boost::make_shared<FileSink>(  
                keywords::file_name = log_file,  
                keywords::target_file_name = config.stem + L"_%Y%m%d_%H%M%S_%5N.log"/*,  
                keywords::time_based_rotation = sinks::file::rotation_at_time_point(0, 0, 0)*/); // <--- cause problem  
            file_sink->set_formatter(&formatter);  
            auto backend = file_sink->locked_backend();  
            backend->set_file_collector(sinks::file::make_collector(  
                keywords::target = config.directory, keywords::max_size = 1024 * 1024 * 1024, // 1GB  
                keywords::min_free_space = 100 * 1024 * 1024, keywords::max_files = 512));  
            backend->scan_for_files();  
            backend->auto_flush(true);  
            backend->set_open_mode(std::ios_base::app);  
            backend->set_auto_newline_mode(sinks::auto_newline_mode::always_insert);  
            core->add_sink(file_sink);  
        }  
        else {  
            DBG_ERROR("Failed to create log!");  
        }  
    }  
  
    core->set_filter(Severity >= config.level);  
}  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2023-06-09 11:26:18 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1584424379  

As any filesystem operation, rotating log files may fail, this is to be expected. From your information, it isn't clear what exactly happened, so I can't tell whether the error is legitimate.  
  
In any case, in order to handle exceptions, you can set an [exception handler](https://www.boost.org/doc/libs/1_82_0/libs/log/doc/html/log/detailed/sink_frontends.html#log.detailed.sink_frontends.basic_services.exception_handling) to the sink frontend.

---

## Comment 2

> Username: debuggingbug  
> Created at: 2023-06-09 12:03:56 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1584468333  

@Lastique I already set exception handler, still can NOT catch the exception.  
  
```cpp  
struct ExceptionHandler {  
    typedef void result_type;  
  
    void operator()(std::exception const& e) const noexcept {  
        OutputDebugStringA(std::format("std::runtime_error: {}\n", e.what()).data());  
    }  
    void operator()(boost::filesystem::filesystem_error const& e) const noexcept {  
        OutputDebugStringA(  
            std::format("boost::filesystem::filesystem_error: {}\n", e.what()).data());  
    }  
};  
  
// catch nothing  
auto core = logging::core::get();  
core->set_exception_handler(  
    boost::log::make_exception_handler<std::exception, boost::filesystem::filesystem_error>(  
        ExceptionHandler()));  
  
// catch nothing  
file_sink->set_exception_handler(  
    boost::log::make_exception_handler<std::exception, boost::filesystem::filesystem_error>(  
        ExceptionHandler()));  
```

---

## Comment 3

> Username: Lastique  
> Created at: 2023-06-09 12:26:49 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1584493910  

If it doesn't catch then the exception is not one of the types you listed. BTW, it doesn't make sense to list `std::exception` first, because it will be caught before `filesystem_error` by the handler function object. Exceptions should be listed from the most specific ones to the most general ones, similar to how you order them in the `catch` blocks.  
  
Also note that `std::format` is not `noexcept`.  
  
Getting back to the problem, you should debug to see what the exception type is and where it is being thrown.

---

## Comment 4

> Username: debuggingbug  
> Created at: 2023-06-09 13:01:34 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1584541341  

Thank you! I'll improve my codes.  
  
From minidump, I see the exception is a boost::filesystem::filesystem_error. Since the crash is on other users' machine, I can't debug.

---

## Comment 5

> Username: Lastique  
> Created at: 2023-06-09 14:14:05 UTC  
> Updated at: 2023-06-09 14:15:37 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1584648618  

For the record, the `basic_formatting_sink_frontend::feed_record` function is here:  
  
https://github.com/boostorg/log/blob/28c6b1da87caec38919bd2d26385676ed15b4ac6/include/boost/log/sinks/basic_sink_frontend.hpp#L436-L480  
  
As you can see, the function does catch all exceptions thrown by `backend.consume()`, so the fact that the exception keeps propagating after that means that the exception handler is not set or that it has rethrown the exception. Or that the exception fails to be caught in the first place, but then this sounds like an ABI issue (e.g. when multiple different versions of `filesystem_error` exist in the process, or the language runtime fails to catch the exception for some other unknown reason).

---

## Comment 6

> Username: debuggingbug  
> Created at: 2023-06-11 07:17:15 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1586051713  

I think I've solved this issue by adding directory to target_file_name.  
  
```cpp  
 keywords::target_file_name =  
                    config.directory / (config.stem + L"_%Y%m%d_%H%M%S_%5N.log")  
```  
  
If the directory is not specified, the new log file will be created on `%ProgramFiles%\<Company>\<ProductName>`, which is **readonly**. See the following code, `m_pImpl->m_TargetStorageDir` is null. `filesystem::create_directories(new_file_name.parent_path());` will throw.  
  
```cpp  
//! The method rotates the file  
BOOST_LOG_API void text_file_backend::rotate_file()  
{  
    filesystem::path prev_file_name = m_pImpl->m_FileName;  
    close_file();  
  
    // Check if the file has been created in the first place  
    system::error_code ec;  
    filesystem::file_status status = filesystem::status(prev_file_name, ec);  
    if (status.type() == filesystem::regular_file)  
    {  
        if (!!m_pImpl->m_TargetFileNameGenerator)  
        {  
            filesystem::path new_file_name = m_pImpl->m_TargetStorageDir / m_pImpl->m_TargetFileNameGenerator(m_pImpl->m_FileCounter);  
  
            if (new_file_name != prev_file_name)  
            {  
                filesystem::create_directories(new_file_name.parent_path());  
                move_file(prev_file_name, new_file_name);  
  
                prev_file_name.swap(new_file_name);  
            }  
        }  
  
        if (!!m_pImpl->m_pFileCollector)  
            m_pImpl->m_pFileCollector->store_file(prev_file_name);  
    }  
}  
```

---

## Comment 7

> Username: UMU618  
> Created at: 2023-06-11 07:23:38 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1586053599  

@Lastique How about specifying `m_TargetStorageDir` as `m_StorageDir`, when `m_TargetStorageDir` is empty?

---

## Comment 8

> Username: Lastique  
> Created at: 2023-06-11 14:38:23 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1586191513  

> I think I've solved this issue by adding directory to target_file_name.  
  
Good, it explains why you were getting an exception. It doesn't explain why the exception wasn't caught, though. Which means, if an exception is thrown for whatever other reason, you will have the same problem again. I'd recommend continuing debugging to fix this. Double-check that you set an exception handler for all sinks. Check if it's actually being called by setting a custom function object with console output or something instead of the one generated by `make_exception_handler`.

---

## Comment 9

> Username: Lastique  
> Created at: 2023-06-11 14:43:01 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1586192636  

> @Lastique How about specifying `m_TargetStorageDir` as `m_StorageDir`, when `m_TargetStorageDir` is empty?  
  
I don't like introducing this kind of non-obvious behavior. Currently, any relative paths you provide to the library are interpreted the same way as anywhere else in the process - they are relative to the current path for the process. Which means, it is probably a good idea to always provide absolute paths. But that doesn't mean it is a good idea to replace the path behind user's back.

---

## Comment 10

> Username: debuggingbug  
> Created at: 2023-06-12 05:29:40 UTC  
> Url: https://github.com/boostorg/log/issues/211#issuecomment-1586608416  

> I don't like introducing this kind of non-obvious behavior.  
  
When I wrote the following codes, I would naturally assume that target_file_name  would be in the same directory as file_name. New log file is temporarily create in m_TargetStorageDir, and will be move to m_StorageDir. So I think this behavior do some good.  
  
```cpp  
keywords::file_name = log_file,  
keywords::target_file_name = config.stem + L"_%Y%m%d_%H%M%S_%5N.log"  
```
