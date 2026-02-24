# #8 - basic_event_log_backend constructor requires keywords::message_file [Closed]

> Username: GregDomjan  
> Created at: 2015-07-16 23:55:28 UTC  
> Updated at: 2015-07-17 17:56:35 UTC  
> Closed at: 2015-07-17 17:56:35 UTC  
> Url: https://github.com/boostorg/log/issues/8  

A bit of a nuisance as I'm not familiar enough with these argument list constructors.  
  
MSVC reports   
`sinks/event_log_backend.hpp(628): error C2679: binary '[' : no operator found which takes a right-hand operand of type 'const boost::parameter::keyword<boost::log::v2s_mt_nt5::keywords::tag::message_file>' (or there is no acceptable conversion)`  
  
https://github.com/boostorg/log/blob/master/include/boost/log/sinks/event_log_backend.hpp#L625-633  
  
Even when `keywords::registration = sinks::event_log::never` and `keywords::message_file` and `keywords::log_name` are unused,  `keywords::message_file` must be provided - perhaps as there is no default for it?  
  
```  
// Create an event log sink  
boost::shared_ptr< sinks::wevent_log_backend > backend(  
    new sinks::wevent_log_backend((  
    keywords::message_file = L"",// "%SystemDir%\\event_log_messages.dll",  not initialising registry, don't need to provide the message dll name - if this isn't provided there is a compiler error here in MSVC  
    //keywords::log_name = L"",// "Application",  not initializing registry, don't need to provide the named log   
    keywords::log_source = L"My Source",  
    keywords::registration = sinks::event_log::never  
    ))  
    );  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2015-07-17 17:56:35 UTC  
> Url: https://github.com/boostorg/log/issues/8#issuecomment-122356899  

Changed in https://github.com/boostorg/log/commit/cb68c029e2352b1ef29d26eeb1073f5f7da8b85a. Thanks.
