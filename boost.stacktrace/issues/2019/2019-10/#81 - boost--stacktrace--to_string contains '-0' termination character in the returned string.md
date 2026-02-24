# #81 - boost::stacktrace::to_string contains '\0' termination character in the returned string [Closed]

> Username: ghoben  
> Created at: 2019-10-16 07:52:20 UTC  
> Updated at: 2019-11-11 17:59:16 UTC  
> Closed at: 2019-11-11 17:59:15 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/81  

I'm using boost version 1_70 on windows.   
  
In case the number of characters for a string representation of a frame exceeds 255 characters, the string returned contains the string termination character '\0' which makes the return string useless/errorful.  
  
Simple call to:   
`std::string backtrace = boost::stacktrace::to_string (boost::stacktrace::stacktrace ());  
`  
The root cause is in **boost\stacktrace\detail\frame_msvc.ipp** method   
```std::string boost::stacktrace::detail::debugging_symbols::get_name_impl(const void* addr, std::string* module_name = 0);  
```  
Starting with Line 200.  
```  
       char name[256];  
        name[0] = '\0';  
        ULONG size = 0;  
        bool res = (S_OK == idebug_->GetNameByOffset(  
            offset,  
            name,  
            sizeof(name),  
            &size,  
            0  
        ));  
        if (!res && size != 0) {  
            result.resize(size);  
            res = (S_OK == idebug_->GetNameByOffset(  
                offset,  
                &result[0],  
                static_cast<ULONG>(result.size()),  
                &size,  
                0  
            ));  
  
```  
In case the original buffer size for `name` (=256)  is too small idebug_->GetNameByOffset() returns false, which calls the method again in L213 given the adjusted size. GetNameByOffset also copies the '\0' termination character into the std::string, which is the problem.   
  
A simple bug fix would be to remove to '\0' termination character from the result after the call successfully completed by adding:  
```  
            if(res) {  
               //The copied string is \0 terminated, we have to remove the \0.  
               result.resize(size-1);  
            }  
```

---

## Comment 1

> Username: ghoben  
> Created at: 2019-10-16 08:00:38 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/81#issuecomment-542575773  

I see, this is fixed on the latest: boost 1_71

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-11-11 17:59:15 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/81#issuecomment-552548300  

Yes, that was fixed in Boost 1.71
