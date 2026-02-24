# #1708 Allow the use of fake timers [Closed]

> Username: reddwarf69  
> Created at: 2019-09-24 07:31:22 UTC  
> Updated at: 2021-02-14 17:10:40 UTC  
> Closed at: 2021-02-14 17:10:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1708  

Related to https://github.com/boostorg/beast/issues/1503  
  
While it would certainly be better to specialise net::basic_waitable_timer I can't see myself doing this any time soon. But by using a silly WaitTraits I can today do  
  
```  
struct my_clock  
{  
  using duration = std::chrono::steady_clock::duration;  
  using rep = duration::rep;  
  using period = duration::period;  
  using time_point = std::chrono::time_point<my_clock>;  
  
  static constexpr bool is_steady = true;  
  
  static time_point current_time;  
  
  static time_point now() noexcept  
  {  
    return current_time;  
  }  
  
  static void move_forward(duration period) {  
    current_time += period;  
  }  
};  
  
my_clock::time_point my_clock::current_time;  
  
struct constant_wait_traits  
{  
  static my_clock::duration to_wait_duration(  
      const my_clock::duration& d)  
  {  
    return my_clock::duration::min();  
  }  
  
  static my_clock::duration to_wait_duration(  
      const my_clock::time_point& t)  
  {  
    return my_clock::duration::min();  
  }  
};  
  
using my_clock_timer = boost::asio::basic_waitable_timer<my_clock, constant_wait_traits>;  
  
using my_basic_stream = beast::basic_stream<asio::local::stream_protocol, boost::asio::executor, boost::beast::unlimited_rate_policy, my_clock_timer>;  
```  
  
and create tests with that.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-09-25 17:28:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-535127590  

Phew... it is interesting to see that this works (it does work for tests, right?) but I don't know how I feel about adding the template parameter. It advertises a lot of open-ended functionality that I am unsure I can stick to. It places additional restrictions on the implementation of the timeout feature. And it puts the clock in users' faces....

---

## Comment 2

> Username: reddwarf69  
> Created_at: 2019-09-25 22:20:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-535246396  

> it does work for tests, right?  
  
I have not really tested it a lot. I didn't want to invest too much time on writing tests when the final interface can change. But, for example, this  
  
```  
#include <array>  
#include <boost/asio/local/stream_protocol.hpp>  
#include <boost/beast/core/basic_stream.hpp>  
#include <boost/beast/websocket/stream.hpp>  
#include <cerrno>  
#include <chrono>  
#include <sys/socket.h>  
#include <system_error>  
#include <iostream>  
#include <tuple>  
  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
  
struct my_clock  
{  
  using duration = std::chrono::steady_clock::duration;  
  using rep = duration::rep;  
  using period = duration::period;  
  using time_point = std::chrono::time_point<my_clock>;  
  
  static constexpr bool is_steady = true;  
  
  static time_point current_time;  
  
  static time_point now() noexcept  
  {  
    return current_time;  
  }  
  
  static void move_forward(duration period) {  
    current_time += period;  
  }  
};  
  
my_clock::time_point my_clock::current_time;  
  
struct constant_wait_traits  
{  
  static my_clock::duration to_wait_duration(  
      const my_clock::duration& d)  
  {  
    return my_clock::duration::min();  
  }  
  
  static my_clock::duration to_wait_duration(  
      const my_clock::time_point& t)  
  {  
    return my_clock::duration::min();  
  }  
};  
  
using my_clock_timer = boost::asio::basic_waitable_timer<my_clock, constant_wait_traits>;  
  
using my_basic_stream = beast::basic_stream<asio::local::stream_protocol, boost::asio::executor, boost::beast::unlimited_rate_policy, my_clock_timer>;  
using websocket_stream = boost::beast::websocket::stream<my_basic_stream, true, my_clock_timer>;  
  
static auto  
socketpair(asio::io_context& local_ioc, asio::io_context& remote_ioc)  
{  
    auto sv = std::array<int, 2>{};  
    if (::socketpair(AF_LOCAL, SOCK_STREAM, 0, sv.data()) == -1) {  
        throw std::system_error(errno, std::system_category(), "socketpair");  
    }  
  
    auto local_socket =  
            my_basic_stream{ local_ioc, asio::local::stream_protocol{}, sv[0] };  
    auto remote_socket =  
            my_basic_stream{ remote_ioc, asio::local::stream_protocol{}, sv[1] };  
  
    return std::make_tuple(std::move(local_socket), std::move(remote_socket));  
}  
  
int main()  
{  
    using namespace std::chrono_literals;  
  
    auto local_ioc = asio::io_context{};  
    auto remote_ioc = asio::io_context{};  
  
    auto [local_socket, remote_socket] = socketpair(local_ioc, remote_ioc);  
  
  
    auto ws_client = websocket_stream{std::move(local_socket)};  
    auto ws_server = websocket_stream{std::move(remote_socket)};  
    auto timeout = beast::websocket::stream_base_clock<my_clock_timer>::timeout{};  
    timeout.handshake_timeout = 3h;  
    timeout.idle_timeout = beast::websocket::stream_base_clock<my_clock_timer>::none();  
    timeout.keep_alive_pings = false;  
    ws_client.set_option(timeout);  
  
    ws_client.async_handshake("lala.com", "/", [](const beast::error_code& ec){std::cout << "Client connected: " << ec.message() << std::endl;});  
    ws_server.async_accept([](const beast::error_code& ec){std::cout << "Server accepted: " << ec.message() << std::endl;});  
  
    my_clock::move_forward(4h);  
  
    local_ioc.poll();  
    remote_ioc.poll();  
    local_ioc.poll();  
}  
```  
  
Output this  
```  
Client connected: The socket was closed due to a timeout  
Server accepted: Broken pipe  
```  
(and Success if the clock is moved forward only 2 hours)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2019-09-26 14:35:12 UTC  
> Updated_at: 2020-07-15 14:30:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-535532535  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1708?src=pr&el=h1) Report  
> Merging [#1708](https://codecov.io/gh/boostorg/beast/pull/1708?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3486e9cb18aa39b392e07031a33e65b1792fbccf&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `90.09%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1708/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1708?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1708      +/-   ##  
===========================================  
- Coverage    95.09%   95.09%   -0.01%       
===========================================  
  Files          153      154       +1       
  Lines        11939    11938       -1       
===========================================  
- Hits         11353    11352       -1       
  Misses         586      586                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1708?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2Jhc2ljX3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/detail/stream\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9zdHJlYW1fYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/rate\_policy.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3JhdGVfcG9saWN5LmhwcA==) | `81.81% <ø> (ø)` | |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/stream\_fwd.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtX2Z3ZC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaHBw) | `74.48% <66.66%> (ø)` | |  
| [include/boost/beast/core/impl/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfc3RyZWFtLmhwcA==) | `85.87% <95.23%> (ø)` | |  
| [include/boost/beast/websocket/impl/accept.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/close.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5ocHA=) | `98.81% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `98.26% <100.00%> (ø)` | |  
| ... and [9 more](https://codecov.io/gh/boostorg/beast/pull/1708/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1708?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1708?src=pr&el=footer). Last update [3486e9c...0c7e9ba](https://codecov.io/gh/boostorg/beast/pull/1708?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: reddwarf69  
> Created_at: 2019-09-26 19:53:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-535661163  

> It advertises a lot of open-ended functionality that I am unsure I can stick to. It places additional restrictions on the implementation of the timeout feature.  
  
I guess you could always move the main implementation to experimental and do something like  
  
```  
beast::experimental::basic_stream<TimeoutTimer, Protocol, Executor, RatePolicy>  
beast::basic_stream<Protocol, Executor, RatePolicy> = experimental::basic_stream<net::steady_timer, Protocol, Executor, RatePolicy>  
```  
  
?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2019-09-27 00:38:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-535735435  

To be honest I'm not feeling it

---

## Comment 6

> Username: reddwarf69  
> Created_at: 2019-09-27 07:15:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-535821440  

Fair enough. But where does this leave https://github.com/boostorg/beast/issues/1503?  
  
There you say `beast::basic_stream needs a Clock template parameter`. The main difference of this approach is that it adds the Clock __AND WaitTraits__ template parameters (not exactly, but easily transformable into that). Is the WaitTraits template parameter the problem (so I/somebody could consider the net::basic_waitable_timer specialization option) or you have simply had a change of heart about the Clock template parameter?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2019-09-27 13:30:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-535939540  

Sometimes I create an issue to serve as a note, or to keep track of an idea that turned out to be not so good

---

## Comment 8

> Username: stale[bot]  
> Created_at: 2019-10-27 13:36:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-546695370  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2019-11-02 15:22:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-549053440  

@chriskohlhoff what do you think about this?

---

## Comment 10

> Username: reddwarf69  
> Created_at: 2019-11-02 22:35:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-549087259  

FWIW there are a couple of thing I'm unsure about this PR:  
- Probably there is a need to export the Timer type of websocket::stream. Otherwise the user may not know what duration type it should use when setting its timeout.  
- You could argue the timer shouldn't be constructed by the objects, but injected in their constructors (with the argument defaulted to a steady_timer). So you could, for example, reuse the same timer used in the tcp_stream for the websocket::stream.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2021-02-14 17:10:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1708#issuecomment-778807420  

I don't think this is a good idea for Beast

---
