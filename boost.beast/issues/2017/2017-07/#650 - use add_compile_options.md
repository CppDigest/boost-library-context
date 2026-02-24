# #650 - use add_compile_options [Closed]

> Username: vinniefalco  
> Created at: 2017-07-14 17:23:50 UTC  
> Updated at: 2017-07-29 07:27:26 UTC  
> Closed at: 2017-07-29 07:27:26 UTC  
> Url: https://github.com/boostorg/beast/issues/650  

e.g.  
```  
if (MSVC)  
    add_compile_options(  
        # Disable warnings  
        -D_CRT_SECURE_NO_WARNINGS  
        -D_SCL_SECURE_NO_WARNINGS  
        /wd4503 # decorated name length exceeded, name was truncated  
        /MP # Multi-processor compilation  
        )  
endif()  
```
