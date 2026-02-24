# #679 - Proposal: Replace ``boost::system::error_*`` with ``std::error_*`` (C++11) [Closed]

> Username: phprus  
> Created at: 2022-02-11 14:55:23 UTC  
> Updated at: 2022-02-11 17:20:52 UTC  
> Closed at: 2022-02-11 17:20:52 UTC  
> Url: https://github.com/boostorg/json/issues/679  

Boost.JSON requires C++11.  
  
``std::error_code``, ``std::error_category``, ``std::error_condition``, ``std::system_error`` are supported in all compilers required for Boost.JSON.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-02-11 17:15:22 UTC  
> Updated at: 2022-02-11 17:15:48 UTC  
> Url: https://github.com/boostorg/json/issues/679#issuecomment-1036432070  

The boost flavors of `error_code`, et. al. are more full-featured than their standard library equivalents. For example, `boost::error_code` is capable of storing the source location where the error was generated. That said, there have been very recent changes to Boost which allow these types to interoperate better with the `std` types. And we are making tweaks to Boost.JSON to let you use the str equivalents more easily. However we can never get rid of the use of `boost` types completely, one because of Asio and two because they have more functionality.

---

## Comment 2

> Username: phprus  
> Created at: 2022-02-11 17:20:52 UTC  
> Url: https://github.com/boostorg/json/issues/679#issuecomment-1036436952  

Thanks for the answer!
