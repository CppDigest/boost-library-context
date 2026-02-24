# #437 - Boost 1.86.0 breaks so many things [Closed]

> Username: catfromplan9  
> Created at: 2024-11-05 04:57:20 UTC  
> Updated at: 2025-01-16 23:07:17 UTC  
> Closed at: 2025-01-16 23:07:17 UTC  
> Url: https://github.com/boostorg/asio/issues/437  

I tried to do a system upgrade on Gentoo today, but dwarfs and citra were both failing to compile. I solved this by downgrading boost to 1.85.0. Here is the errors for citra, if you're curious.  
  
```  
In file included from /usr/include/boost/asio.hpp:69,  
                 from /var/tmp/portage/games-emulation/citra-20241012/work/citra-cdce02607abf1b43cdf93c6f367825158f23a467/src/input_common/udp/client.cpp:11:  
/usr/include/boost/asio/co_composed.hpp:849:13: error: ‘async_operation’ has not been declared  
  849 |   template <async_operation Op>  
      |             ^~~~~~~~~~~~~~~  
/usr/include/boost/asio/co_composed.hpp:850:24: error: ‘Op’ has not been declared  
  850 |   auto await_transform(Op&& op  
      |                        ^~  
/usr/include/boost/asio/co_composed.hpp: In member function ‘auto boost::asio::detail::co_composed_promise<Executors, Handler, Return>::await_transform(int&&)’:  
/usr/include/boost/asio/co_composed.hpp:862:19: error: expected ‘)’ before ‘&&’ token  
  862 |       awaitable(Op&& op, co_composed_promise& promise  
      |                   ^~  
/usr/include/boost/asio/co_composed.hpp:862:16: note: to match this ‘(’  
  862 |       awaitable(Op&& op, co_composed_promise& promise  
      |                ^  
/usr/include/boost/asio/co_composed.hpp:915:7: error: ‘Op’ does not name a type  
  915 |       Op&& op_;  
      |       ^~  
/usr/include/boost/asio/co_composed.hpp:925:34: error: ‘Op’ was not declared in this scope; did you mean ‘op’?  
  925 |     return awaitable{static_cast<Op&&>(op), *this  
      |                                  ^~  
      |                                  op  
/usr/include/boost/asio/co_composed.hpp:925:21: error: expected primary-expression before ‘{’ token  
  925 |     return awaitable{static_cast<Op&&>(op), *this  
      |                     ^  
/usr/include/boost/asio/co_composed.hpp:925:21: error: expected ‘;’ before ‘{’ token  
/usr/include/boost/asio/co_composed.hpp:925:34: error: ‘Op’ does not name a type  
  925 |     return awaitable{static_cast<Op&&>(op), *this  
      |                                  ^~  
/usr/include/boost/asio/co_composed.hpp:925:36: error: expected ‘>’ before ‘&&’ token  
  925 |     return awaitable{static_cast<Op&&>(op), *this  
      |                                    ^~  
/usr/include/boost/asio/co_composed.hpp:925:36: error: expected ‘(’ before ‘&&’ token  
/usr/include/boost/asio/co_composed.hpp:925:38: error: expected identifier before ‘>’ token  
  925 |     return awaitable{static_cast<Op&&>(op), *this  
      |                                      ^  
/usr/include/boost/asio/co_composed.hpp:931:7: error: expected ‘)’ before ‘}’ token  
  931 |       };  
      |       ^  
```  
  
Please fix your software.
