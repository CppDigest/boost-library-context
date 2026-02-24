# #1484 - Advanced flex server example checks for wrong timeout error code? [Closed]

> Username: Eelis  
> Created at: 2019-02-28 15:36:48 UTC  
> Updated at: 2019-02-28 19:43:28 UTC  
> Closed at: 2019-02-28 19:43:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1484  

Consider the following lines in `http_session::on_write` in `advanced_server_flex.cpp`:  
  
    // Happens when the timer closes the socket  
    if(ec == net::error::operation_aborted)  
        return;  
  
I guess "the timer" now refers to the timer inside the tcp_stream, but if so, according to the [tcp_stream documentation](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__basic_stream.html), the error code to check for is `timeout`, not `operation_aborted`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-28 15:40:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1484#issuecomment-468320031  

You got some eagle eyes there... "the timer" refers to the broken timer code which I removed from advanced-server-flex, and I forgot to delete that code you quoted. Old code:  
  
https://github.com/boostorg/beast/blob/boost-1.69.0/example/advanced/server-flex/advanced_server_flex.cpp#L291  
  
"the timer"  
https://github.com/boostorg/beast/blob/boost-1.69.0/example/advanced/server-flex/advanced_server_flex.cpp#L247  
  
I'm pretty sure this is broken in 1.69, timers are hard to get right and thus the motivation for building it directly into `tcp_stream` and `websocket::stream`.  
  
You really should join us on Slack, https://slack.cpp.al for the invite

---

## Comment 2

> Username: Eelis  
> Created at: 2019-02-28 15:51:49 UTC  
> Updated at: 2019-02-28 15:52:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1484#issuecomment-468324755  

Yep, this morning I decided I don't want to wait for 1.70 anymore, and will use Boost master for a few weeks in order to start taking advantage of all the recent Beast improvements. And since my application is based on the advanced flex server example, that's the part of the Beast code that I end up studying closest, to find out the updated recommended ways of doing things. :)
