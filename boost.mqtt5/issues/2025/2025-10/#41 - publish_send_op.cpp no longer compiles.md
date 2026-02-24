# #41 - publish_send_op.cpp no longer compiles [Closed]

> Username: pdimov  
> Created at: 2025-10-29 17:14:29 UTC  
> Updated at: 2025-10-29 19:56:39 UTC  
> Closed at: 2025-10-29 19:56:39 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/41  

```  
[ 86%] Building CXX object libs/mqtt5/test/CMakeFiles/boost_mqtt5-tests.dir/unit/publish_send_op.cpp.o  
/home/runner/work/boost/boost/libs/mqtt5/test/unit/logger.cpp: In member function ‘boost::asio::ip::basic_resolver<boost::asio::ip::tcp>::results_type logger_tests::resolve_test_data::endpoints()’:  
/home/runner/work/boost/boost/libs/mqtt5/test/unit/logger.cpp:106:25: error: ‘system_executor’ is not a member of ‘boost::asio’; did you mean ‘uses_executor’?  
  106 |         auto ex = asio::system_executor {};  
      |                         ^~~~~~~~~~~~~~~  
      |                         uses_executor  
```  
  
Looks like the latest and greatest Asio changes have broken it.
