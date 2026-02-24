# #95 - Windows custom printing very slow [Closed]

> Username: nminkov  
> Created at: 2020-07-08 13:17:00 UTC  
> Updated at: 2020-12-15 14:19:13 UTC  
> Closed at: 2020-12-15 14:19:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/95  

Manually iterating frames and accessing name, source file, source line is extremely slow. However printing all the frames with default function out stream is not. By slow, I mean several seconds for each frame function access.   
  
After cheching the code I can see that boost::stacktrace::detail::debugging_symbols  object is created for every and each call to name(), source_name(), source_line() which simply unusable in real world app.   
  
```  
std::string frame::name() const {  
    boost::stacktrace::detail::debugging_symbols idebug;  
    return idebug.get_name_impl(addr_);  
}  
  
  
std::string frame::source_file() const {  
    boost::stacktrace::detail::debugging_symbols idebug;  
    return idebug.get_source_file_line_impl(addr_).first;  
}  
  
std::size_t frame::source_line() const {  
    boost::stacktrace::detail::debugging_symbols idebug;  
    return idebug.get_line_impl(addr_);  
}  
  
std::string to_string(const frame& f) {  
    std::string res;  
  
    boost::stacktrace::detail::debugging_symbols idebug;  
    idebug.to_string_impl(f.address(), res);  
    return res;  
}  
```  
  
I would highly suggest to cache the frame data on frame creation, with single boost::stacktrace::detail::debugging_symbols object.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-11-18 19:07:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/95#issuecomment-729892206  

Try using `BOOST_STACKTRACE_USE_WINDBG_CACHED` http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html  
  
Please report back: is it helped or not
