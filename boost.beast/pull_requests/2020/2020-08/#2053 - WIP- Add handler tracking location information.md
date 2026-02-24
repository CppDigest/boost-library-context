# #2053 WIP: Add handler tracking location information [Closed]

> Username: chriskohlhoff  
> Created at: 2020-08-16 02:04:06 UTC  
> Updated at: 2020-08-29 09:39:52 UTC  
> Closed at: 2020-08-29 09:39:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2053  

These changes use the new `BOOST_ASIO_HANDLER_LOCATION` macro to add more context to handler tracking. For example, the following is a snippet of the output produced by the http-client-async-ssl example:  
  
```  
@asio|1597543084.233257|>33|  
@asio|1597543084.233273|33|deadline_timer@0x7fa6cac25218.cancel  
@asio|1597543084.233681|33^34|in 'basic_stream::async_write_some' (../../../../../../boost/beast/core/impl/basic_stream.hpp:321)  
@asio|1597543084.233681|33^34|called from 'async_write' (../../../../../../boost/asio/impl/write.hpp:331)  
@asio|1597543084.233681|33^34|called from 'ssl::stream<>::async_write_some' (../../../../../../boost/asio/ssl/detail/io.hpp:201)  
@asio|1597543084.233681|33^34|called from 'http::async_write_some' (../../../../../../boost/beast/http/impl/write.hpp:64)  
@asio|1597543084.233681|33^34|called from 'http::async_write' (../../../../../../boost/beast/http/impl/write.hpp:223)  
@asio|1597543084.233681|33^34|called from 'http::async_write(msg)' (../../../../../../boost/beast/http/impl/write.hpp:277)  
@asio|1597543084.233681|33*34|deadline_timer@0x7fa6cac25298.async_wait  
@asio|1597543084.233801|33^35|in 'basic_stream::async_write_some' (../../../../../../boost/beast/core/impl/basic_stream.hpp:373)  
@asio|1597543084.233801|33^35|called from 'async_write' (../../../../../../boost/asio/impl/write.hpp:331)  
@asio|1597543084.233801|33^35|called from 'ssl::stream<>::async_write_some' (../../../../../../boost/asio/ssl/detail/io.hpp:201)  
@asio|1597543084.233801|33^35|called from 'http::async_write_some' (../../../../../../boost/beast/http/impl/write.hpp:64)  
@asio|1597543084.233801|33^35|called from 'http::async_write' (../../../../../../boost/beast/http/impl/write.hpp:223)  
@asio|1597543084.233801|33^35|called from 'http::async_write(msg)' (../../../../../../boost/beast/http/impl/write.hpp:277)  
@asio|1597543084.233801|33*35|socket@0x7fa6cac251c8.async_send  
@asio|1597543084.233910|.35|non_blocking_send,ec=system:0,bytes_transferred=103  
@asio|1597543084.233949|<33|  
@asio|1597543084.233983|<31|  
@asio|1597543084.234031|>30|ec=system:89  
@asio|1597543084.234045|30*36|strand_executor@0x7fa6cac24bd0.execute  
@asio|1597543084.234054|>36|  
@asio|1597543084.234064|<36|  
@asio|1597543084.234072|<30|  
@asio|1597543084.234086|>35|ec=system:0,bytes_transferred=103  
@asio|1597543084.234100|35*37|strand_executor@0x7fa6cac24bd0.execute  
@asio|1597543084.234109|>37|  
@asio|1597543084.234119|37|deadline_timer@0x7fa6cac25298.cancel  
@asio|1597543084.234198|37^38|in 'basic_stream::async_read_some' (../../../../../../boost/beast/core/impl/basic_stream.hpp:321)  
@asio|1597543084.234198|37^38|called from 'ssl::stream<>::async_read_some' (../../../../../../boost/asio/ssl/detail/io.hpp:168)  
@asio|1597543084.234198|37^38|called from 'http::async_read_some' (../../../../../../boost/beast/http/impl/read.hpp:212)  
@asio|1597543084.234198|37^38|called from 'http::async_read' (../../../../../../boost/beast/http/impl/read.hpp:297)  
@asio|1597543084.234198|37^38|called from 'http::async_read(msg)' (../../../../../../boost/beast/http/impl/read.hpp:101)  
@asio|1597543084.234198|37*38|deadline_timer@0x7fa6cac25218.async_wait  
@asio|1597543084.234288|37^39|in 'basic_stream::async_read_some' (../../../../../../boost/beast/core/impl/basic_stream.hpp:373)  
@asio|1597543084.234288|37^39|called from 'ssl::stream<>::async_read_some' (../../../../../../boost/asio/ssl/detail/io.hpp:168)  
@asio|1597543084.234288|37^39|called from 'http::async_read_some' (../../../../../../boost/beast/http/impl/read.hpp:212)  
@asio|1597543084.234288|37^39|called from 'http::async_read' (../../../../../../boost/beast/http/impl/read.hpp:297)  
@asio|1597543084.234288|37^39|called from 'http::async_read(msg)' (../../../../../../boost/beast/http/impl/read.hpp:101)  
@asio|1597543084.234288|37*39|socket@0x7fa6cac251c8.async_receive  
@asio|1597543084.234334|.39|non_blocking_recv,ec=system:35,bytes_transferred=0  
@asio|1597543084.234353|<37|  
@asio|1597543084.234364|<35|  
@asio|1597543084.234380|>34|ec=system:89  
@asio|1597543084.234392|34*40|strand_executor@0x7fa6cac24bd0.execute  
@asio|1597543084.234401|>40|  
@asio|1597543084.234408|<40|  
@asio|1597543084.234416|<34|  
@asio|1597543084.427594|.39|non_blocking_recv,ec=system:0,bytes_transferred=534  
@asio|1597543084.427680|>39|ec=system:0,bytes_transferred=534  
```  
  
Marked as work-in-progress as this PR doesn't currently cover the websocket implementation. I can look at that if there's interest in this feature.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-08-16 06:17:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2053#issuecomment-674485348  

Oh interesting. Thank you.

---

## Review 2 [Commented]

> Username: madmongo1  
> Created_at: 2020-08-21 14:16:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2053#pullrequestreview-472528345  

The presence of optional diagnostic logging never hurts.  
I don't think the addition of the logging code obscures Beast's logic too much.  
I guess the question is whether our users will benefit from its presence.  
  
Chris do you have experience of users guiding themselves to fixing bugs with asio logging, or is it intended more for maintainers?

---

## Comment 3

> Username: chriskohlhoff  
> Created_at: 2020-08-21 14:35:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2053#issuecomment-678323637  

Handler tracking seems to crop up in stackoverflow questions now and then, e.g.:  
  
https://stackoverflow.com/questions/36681480/boostasio-async-wait-in-async-accept-handler-crashes-application  
  
and in this context the tracking output can help people solve other users' issues.  
  
As of boost 1.74, asio also includes scripts that help users find the unfinished operations in the handler tracking output, so the additional context helps there as well.  
  
On Sat, Aug 22, 2020, at 12:17 AM, Richard Hodges wrote:  
> Chris do you have experience of users guiding themselves to fixing bugs   
> with asio logging, or is it intended more for maintainers?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-08-21 14:54:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2053#issuecomment-678333170  

This has to go into all the async operations, including icy_stream for example.

---

## Comment 5

> Username: madmongo1  
> Created_at: 2020-08-21 20:37:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2053#issuecomment-678482260  

OK, Ill add commits to that effect. Thank you Chris.

---
