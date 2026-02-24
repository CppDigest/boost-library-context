# #57 - Fast conversion of stacktraces to std::string [Closed]

> Username: antoshkka1  
> Created at: 2018-07-27 11:44:10 UTC  
> Updated at: 2018-07-28 14:12:11 UTC  
> Closed at: 2018-07-28 14:12:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/57  

```  
/// Outputs stacktrace in a human readable format to std::string; unsafe to use in async handlers.  
template <class Allocator>  
std::string to_string(const basic_stacktrace<Allocator>& bt) {  
    if (bt) {  
        return boost::stacktrace::detail::to_string(&bt.as_vector()[0], bt.size());  
    } else {  
        return std::string();  
    }  
}  
```
