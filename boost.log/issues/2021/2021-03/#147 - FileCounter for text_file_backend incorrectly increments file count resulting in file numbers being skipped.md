# #147 - FileCounter for text_file_backend incorrectly increments file count resulting in file numbers being skipped [Closed]

> Username: chrisjaquet  
> Created at: 2021-03-29 14:36:02 UTC  
> Updated at: 2021-03-30 05:41:46 UTC  
> Closed at: 2021-03-29 15:03:28 UTC  
> Url: https://github.com/boostorg/log/issues/147  

I think I have encountered a bug in the way file numbering is handled in the `sinks::text_file_backend`. I have not tested other sinks.  
  
Found in Boost 1.71.0 but the code appears unchanged in Boost 1.75.0 and in the [log library @ 8a88072](https://github.com/boostorg/log/tree/8a880720be5a990fdd0f864244bfff1a39a69d83).  
  
  
I set up my text file sink using the following  
  
```  
auto textFileSinkPtr =   
    boost::make_shared<boost::log::sinks::synchronous_sink<boost::log::sinks::text_file_backend>>(  
        boost::log::keywords::file_name = "MyApp.log",  
        boost::log::keywords::target_file_name = "MyApp_%N.log",  
        boost::log::keywords::rotation_size = 5 * 1024 * 1024,  
        boost::log::keywords::time_based_rotation = boost::log::sinks::file::rotation_at_time_point(0, 0, 0),  
        boost::log::keywords::open_mode = std::ios_base::out | std::ios_base::app  
    );  
...  
textFileSinkPtr->locked_backend()->scan_for_files();  
```  
however the log files that are generated have the suffix 1, 3, 5, etc.  
  
I think that this is caused by the following code in [`libs/log/src/text_file_backend.cpp`](https://github.com/boostorg/log/blob/8a880720be5a990fdd0f864244bfff1a39a69d83/src/text_file_backend.cpp) in the `void text_file_backend::consume(record_view const& rec, string_type const& formatted_message)` method:  
  
```  
if (!use_prev_file_name)  
    new_file_name = m_pImpl->m_StorageDir / m_pImpl->m_FileNameGenerator(m_pImpl->m_FileCounter++);  
else  
    prev_file_name.swap(new_file_name);  
```  
  
where  `m_pImpl->m_FileCounter` is incremented, even when m_pImpl->m_FileNameGenerator() returns `MyApp.log` as the resulting `new_file_name`.   
  
When `BOOST_LOG_API void text_file_backend::rotate_file()` is called at a later stage the updated (post-incremented) value of `m_FileCounter` is used in the following block of code instead of the value before incrementation:  
  
```  
    if (!!m_pImpl->m_TargetFileNameGenerator)  
    {  
        filesystem::path new_file_name;  
        new_file_name = m_pImpl->m_TargetStorageDir / m_pImpl->m_TargetFileNameGenerator(m_pImpl->m_FileCounter);  
  
        if (new_file_name != prev_file_name)  
        {  
            filesystem::create_directories(new_file_name.parent_path());  
            move_file(prev_file_name, new_file_name);  
  
            prev_file_name.swap(new_file_name);  
        }  
    }  
```  
resulting in the file number being incremented by 1 too many.  
  
This occurs when the `rotate_file()` call is done as part of shutting down the logging at application exit (and no log rotations have happened as part of the normal execution of the application).

---

## Comment 1

> Username: Lastique  
> Created at: 2021-03-29 15:03:28 UTC  
> Url: https://github.com/boostorg/log/issues/147#issuecomment-809454032  

I cannot reproduce this with the current develop.  
  
The `rotate_file` code you quoted is incorrect, as it explicitly decrements the counter to account for the earlier increment in `consume`.  
  
https://github.com/boostorg/log/blob/8a880720be5a990fdd0f864244bfff1a39a69d83/src/text_file_backend.cpp#L1494-L1495

---

## Comment 2

> Username: chrisjaquet  
> Created at: 2021-03-30 05:41:46 UTC  
> Url: https://github.com/boostorg/log/issues/147#issuecomment-809927238  

Apologies - I missed the updated `rotate_file` code. Thanks.
