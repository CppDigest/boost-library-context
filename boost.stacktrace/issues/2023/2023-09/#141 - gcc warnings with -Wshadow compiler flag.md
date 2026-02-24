# #141 - gcc warnings with -Wshadow compiler flag [Closed]

> Username: nigels-com  
> Created at: 2023-09-05 23:28:28 UTC  
> Updated at: 2024-06-11 09:21:28 UTC  
> Closed at: 2024-06-11 09:21:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/141  

Looking at `safe_dump_to.hpp` there is a recurring pattern there of doing:  
  
```  
        typedef boost::stacktrace::detail::native_frame_ptr_t native_frame_ptr_t;  
```  
  
Which is understandable, on the face of it, improving clarity.  
  
On the other hand, our codebase is using -Wshadow compiler flag on Linux and it is complaining about this in `boost::stacktrace` eventhough it's arguably a false alarm.  
  
But looking more closely, we're already in the `boost::stacktrace::detail` namespace, are these additional typedefs still necessary?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-06-11 09:21:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/141#issuecomment-2160220338  

Many thanks for the report!
