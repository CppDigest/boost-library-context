# #196 - Tests for !BOOST_ASIO_HAS_LOCAL_SOCKETS [Closed]

> Username: anarthal  
> Created at: 2023-12-20 12:23:17 UTC  
> Updated at: 2024-07-14 19:51:34 UTC  
> Closed at: 2024-07-14 19:51:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/196  

All our CI systems have `BOOST_ASIO_HAS_LOCAL_SOCKETS` defined, but some legacy systems may not support it. Include a build that makes `BOOST_ASIO_HAS_LOCAL_SOCKETS` false.
