# #18 - Seg fault in example terminate_handler.cpp [Closed]

> Username: pete-johnson  
> Created at: 2017-05-11 16:07:03 UTC  
> Updated at: 2017-05-16 07:31:10 UTC  
> Closed at: 2017-05-16 07:31:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/18  

In environment Windows 7 x64, VS2017, boost 1.64.  
  
Running the example 'terminate_handler.cpp' generates an access violation seg fault.  
  
line 27 of stacktrace/detail/safe_dump_win.ipp has '0' as the penultimate parameter in the call to WriteFile. This should be a pointer to a DWORD for the WriteFile function to write the number of bytes written to file.  
  
Changing this line to these two:  
`	boost::detail::winapi::DWORD_ written =0;  
    if (!boost::detail::winapi::WriteFile(fd, frames, static_cast<boost::detail::winapi::DWORD_>(sizeof(native_frame_ptr_t) * frames_count), &written, 0)) {  
`  
resolves the seg fault.  
  
Pete

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-05-16 07:31:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/18#issuecomment-301699625  

Great thanks for locating this issue and proposing a fix! I've been seeing random crashes for a few weeks but could not locate the source of crashes.  
  
Great thanks!
