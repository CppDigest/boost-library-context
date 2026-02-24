# #2001 - Does write_limit only for TCP? [Closed]

> Username: GOOD-Stuff  
> Created at: 2020-06-29 17:04:23 UTC  
> Updated at: 2020-06-30 07:29:13 UTC  
> Closed at: 2020-06-29 19:59:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2001  

Hello everyone!  
  
My current Beast version is: `#define BOOST_BEAST_VERSION 290`  
  
Do I understand correctly that `simple_rate_policy::write_limit(std::size_t bytes_per_second)` work only for TCP stream?  
Can I use the `beast::basic_stream` as follows:  
```C++  
boost::beast::basic_stream<boost::asio::ip::udp,  
                           boost::asio::executor,  
                           boost::beast::simple_rate_policy> stream(ioc);  
```  
or `beast::basic_stream` (in async way) working only for TCP protocol?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-29 17:21:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2001#issuecomment-651254559  

Only TCP

---

## Comment 2

> Username: GOOD-Stuff  
> Created at: 2020-06-29 19:59:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2001#issuecomment-651328200  

Ok, thanks.
