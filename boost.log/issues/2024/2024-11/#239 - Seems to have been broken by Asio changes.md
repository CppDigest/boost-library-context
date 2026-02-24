# #239 - Seems to have been broken by Asio changes [Closed]

> Username: pdimov  
> Created at: 2024-11-05 14:57:01 UTC  
> Updated at: 2024-11-05 15:18:14 UTC  
> Closed at: 2024-11-05 15:17:53 UTC  
> Url: https://github.com/boostorg/log/issues/239  

```  
gcc.compile.c++ bin.v2/libs/log/build/gcc-9/release/x86_64/link-static/visibility-hidden/syslog_backend.o  
libs/log/src/syslog_backend.cpp: In member function ‘void boost::log::v2s_st::sinks::syslog_backend::set_local_address(const string&, short unsigned int)’:  
libs/log/src/syslog_backend.cpp:558:49: error: ‘from_string’ is not a member of ‘boost::asio::ip::address’  
  558 |     set_local_address(boost::asio::ip::address::from_string(addr), port);  
      |                                                 ^~~~~~~~~~~  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-11-05 15:18:12 UTC  
> Url: https://github.com/boostorg/log/issues/239#issuecomment-2457449456  

Thanks for letting me know, should be fixed in develop.
