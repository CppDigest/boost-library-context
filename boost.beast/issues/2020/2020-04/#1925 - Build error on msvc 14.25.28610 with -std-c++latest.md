# #1925 - Build error on msvc 14.25.28610 with /std:c++latest [Closed]

> Username: iceboy233  
> Created at: 2020-04-30 05:17:52 UTC  
> Updated at: 2020-05-01 05:16:34 UTC  
> Closed at: 2020-05-01 05:16:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1925  

With beast 1.73.0, `/std:c++17` builds fine, but `/std:c++latest` fails with error message:  
  
```  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): error C2244: 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(782): note: see declaration of 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const Protocol::endpoint &,ConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,ConnectCondition,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,ConnectCondition,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(785): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const Protocol::endpoint &,ConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): error C2244: 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(802): note: see declaration of 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,ConnectCondition,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,ConnectCondition,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(805): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const Protocol::endpoint &,ConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): error C2244: 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(824): note: see declaration of 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,ConnectCondition,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,ConnectCondition,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,ConnectCondition,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(828): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const Protocol::endpoint &,ConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): error C2244: 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(845): note: see declaration of 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,ConnectCondition,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,ConnectCondition,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(848): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const Protocol::endpoint &,ConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): error C2244: 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(866): note: see declaration of 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,ConnectCondition,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,ConnectCondition,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): note: '::boost::asio::async_result<::boost::asio::decay<IteratorConnectHandler>::type,void(boost::beast::error_code,Iterator)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(Iterator,Iterator,IteratorConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,ConnectCondition,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): note: '::boost::asio::async_result<::boost::asio::decay<RangeConnectHandler>::type,void(boost::beast::error_code,Protocol::endpoint)>::return_type boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const EndpointSequence &,RangeConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(870): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect(const Protocol::endpoint &,ConnectHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(890): error C2244: 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_read_some': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(887): note: see declaration of 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_read_some'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(890): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(890): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_read_some(const MutableBufferSequence &,ReadHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(890): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(890): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_read_some(const MutableBufferSequence &,ReadHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(910): error C2244: 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_write_some': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(907): note: see declaration of 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_write_some'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(910): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(910): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_write_some(const ConstBufferSequence &,WriteHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(910): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/basic_stream.hpp(910): note: 'auto boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_write_some(const ConstBufferSequence &,WriteHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(150): error C2244: 'boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_write_some': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(147): note: see declaration of 'boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_write_some'  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(150): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(150): note: 'auto boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_write_some(const ConstBufferSequence &,WriteHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(150): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(150): note: 'auto boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_write_some(const ConstBufferSequence &,WriteHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(221): error C2244: 'boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_read_some': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(218): note: see declaration of 'boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_read_some'  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(221): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(221): note: 'auto boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_read_some(const MutableBufferSequence &,ReadHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(221): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/buffered_read_stream.hpp(221): note: 'auto boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_read_some(const MutableBufferSequence &,ReadHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(153): error C2244: 'boost::beast::flat_stream<NextLayer>::async_read_some': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(150): note: see declaration of 'boost::beast::flat_stream<NextLayer>::async_read_some'  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(153): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(153): note: 'auto boost::beast::flat_stream<NextLayer>::async_read_some(const MutableBufferSequence &,ReadHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(153): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(153): note: 'auto boost::beast::flat_stream<NextLayer>::async_read_some(const MutableBufferSequence &,ReadHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(234): error C2244: 'boost::beast::flat_stream<NextLayer>::async_write_some': unable to match function definition to an existing declaration  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(231): note: see declaration of 'boost::beast::flat_stream<NextLayer>::async_write_some'  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(234): note: definition  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(234): note: 'auto boost::beast::flat_stream<NextLayer>::async_write_some(const ConstBufferSequence &,WriteHandler &&)'  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(234): note: existing declarations  
external/org_boost_beast/include\boost/beast/core/impl/flat_stream.hpp(234): note: 'auto boost::beast::flat_stream<NextLayer>::async_write_some(const ConstBufferSequence &,WriteHandler &&)'  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-30 11:48:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1925#issuecomment-621783439  

Related: #1916   
  
I am testing a fix at the moment.
