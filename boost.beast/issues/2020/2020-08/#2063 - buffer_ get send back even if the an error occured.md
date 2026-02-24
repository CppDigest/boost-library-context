# #2063 - buffer_ get send back even if the an error occured [Closed]

> Username: schorsch1976  
> Created at: 2020-08-25 08:17:28 UTC  
> Updated at: 2020-08-29 09:39:52 UTC  
> Closed at: 2020-08-29 09:39:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2063  

https://github.com/boostorg/beast/blob/201105e66ab738bd8e027cff8c11ddd7cd5c7587/example/advanced/server-flex/advanced_server_flex.cpp#L324  
  
buffer_ get send back even if the an error occured.   
  
Instead of  
```  
        if(ec)  
            fail(ec, "read");  
```  
  
it should be   
```  
        if(ec)  
            return fail(ec, "read");  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-25 09:02:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2063#issuecomment-679900828  

Thanks for this. Great catch!  
  
If you'd like your name on beast, feel free to send in a PR.  
  
If not, I'm happy to fix it.
