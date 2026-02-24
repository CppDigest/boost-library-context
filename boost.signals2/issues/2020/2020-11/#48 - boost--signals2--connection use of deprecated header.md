# #48 - boost::signals2::connection  use of deprecated header [Closed]

> Username: dkruempe  
> Created at: 2020-11-15 19:55:07 UTC  
> Updated at: 2020-11-15 21:22:19 UTC  
> Closed at: 2020-11-15 21:22:19 UTC  
> Url: https://github.com/boostorg/signals2/issues/48  

Hi,  
during the compile time of the current boost library 1.74 I get the following message.  
  
In file included from /usr/local/include/boost/signals2.hpp:19:  
In file included from /usr/local/include/boost/signals2/signal.hpp:22:  
In file included from /usr/local/include/boost/signals2/connection.hpp:22:  
In file included from /usr/local/include/boost/signals2/detail/null_output_iterator.hpp:14:  
/usr/local/include/boost/function_output_iterator.hpp:14:1: warning: This header is deprecated. Use <boost/iterator/function_output_iterator.hpp> instead. [-W#pragma-messages]  
BOOST_HEADER_DEPRECATED("<boost/iterator/function_output_iterator.hpp>")

---

## Comment 1

> Username: fmhess  
> Created at: 2020-11-15 21:22:19 UTC  
> Url: https://github.com/boostorg/signals2/issues/48#issuecomment-727639039  

Duplicate of #45
