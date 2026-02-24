# #398 - Add enum status_code [Closed]

> Username: vinniefalco  
> Created at: 2017-05-29 23:24:41 UTC  
> Updated at: 2017-06-03 22:07:34 UTC  
> Closed at: 2017-06-03 22:07:34 UTC  
> Url: https://github.com/boostorg/beast/issues/398  

Similar to `method` we might want `enum class status` or just `enum status`.  
This means `status` might need accessor functions instead of being a public data member.  
https://hyper.rs/hyper/master/hyper/status/enum.StatusCode.html  
  
Thinking this should be a strongly type `enum class` and use these declarations:  
```  
enum class status_code { ... };  
  
template<class Fields>  
struct header<false, Fields>  
{  
    ...  
    /// Set the status code  
    void status(int);  
    void status(status_code);  
  
    /// Returns the status code  
    status_code status() const;  
    int status_int() const;  
};  
```  
  
We also need `operator<<` for status, perhaps an internal function `to_string(status_code)` which produces the default reason phrases. Finally, the serializer can automatically insert the standard reason for the status code when the reason in the message is the empty string.
