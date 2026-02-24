# #47 fully qualify handler_type<> template in BOOST_ASIO_HANDLER_TYPE macro [Closed]

> Username: leeter  
> Created at: 2016-11-12 21:51:20 UTC  
> Updated at: 2017-12-02 07:36:08 UTC  
> Closed at: 2017-12-02 07:36:08 UTC  
> Url: https://github.com/boostorg/asio/pull/47  

This allows developers to use the handler verification macros in custom services and streams, without this they will receive an error unless they define their code in the `::boost::asio` namespace or do a `using namespace boost::asio;` neither of which are ideal.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-11-17 13:05:30 UTC  
> Url: https://github.com/boostorg/asio/pull/47#issuecomment-261241708  

Good change :+1:

---

## Comment 2

> Username: leeter  
> Created_at: 2017-05-18 12:39:11 UTC  
> Url: https://github.com/boostorg/asio/pull/47#issuecomment-302391511  

@chriskohlhoff any chance of this getting merged?

---

## Comment 3

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:36:08 UTC  
> Url: https://github.com/boostorg/asio/pull/47#issuecomment-348674993  

Fixed on develop.

---
