# #59 - boost::log does not build with -DBOOST_ASIO_NO_DEPRECATED [Closed]

> Username: kivadiu  
> Created at: 2018-08-02 21:55:20 UTC  
> Updated at: 2018-10-21 19:24:35 UTC  
> Closed at: 2018-10-21 19:24:35 UTC  
> Url: https://github.com/boostorg/log/issues/59  

The library uses boost::asio but did not update to the new ASIO API. For example io_service -> io_context.  
For example:  
```  
libs/log/src/syslog_backend.cpp:300:52: error: expected « ) » before « & » token  
         explicit syslog_udp_socket(asio::io_service& service, asio::ip::udp const& protocol, asio::ip::udp::endpoint const& local_address) :  
                                   ~                ^  
                                                    )  
libs/log/src/syslog_backend.cpp:332:15: error: « io_service » dans l'espace de noms « boost::asio » ne nomme pas un type  
         asio::io_service m_IOService;  
               ^~~~~~~~~~  
```  
The new API was introduced in 1.66.0 which deprecated the old interface.
