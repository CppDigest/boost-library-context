# #95 - basic_resolver_results has a wrong typedef to basic_resolver_entry [Closed]

> Username: qwertzui11  
> Created at: 2018-03-20 06:10:12 UTC  
> Updated at: 2018-04-17 09:17:38 UTC  
> Closed at: 2018-04-17 09:17:37 UTC  
> Url: https://github.com/boostorg/asio/issues/95  

`ip::basic_resolver_results` has a wrong `typedef`  
instead of `typedef basic_resolver_entry<endpoint_type> value_type;` it should be corrected to  
`typedef basic_resolver_entry<protocol_type> value_type;`  
https://github.com/boostorg/asio/blob/a67dd907084916de2eacc805036a00609f92c9ea/include/boost/asio/ip/basic_resolver_results.hpp#L66  
the `ip::basic_resolver_iterator` got it right  
https://github.com/boostorg/asio/blob/a67dd907084916de2eacc805036a00609f92c9ea/include/boost/asio/ip/basic_resolver_iterator.hpp#L59  
`basic_resolver_entry` doesn't need an endpoint as template parameter  
https://github.com/boostorg/asio/blob/a67dd907084916de2eacc805036a00609f92c9ea/include/boost/asio/ip/basic_resolver_entry.hpp#L37  
  
boost 1.66  
  
If I can be any more help, pls let me know  
Have fun  
Markus

---

## Comment 1

> Username: qwertzui11  
> Created at: 2018-04-17 09:17:37 UTC  
> Url: https://github.com/boostorg/asio/issues/95#issuecomment-381915573  

`Fixed incorrect basic_resolver_results::value_type typedef.`  
https://www.boost.org/users/history/version_1_67_0.html  
looks like it got resolved
