# #746 - Wrong number of threads in server examples [Closed]

> Username: dlaundon  
> Created at: 2017-08-18 11:29:29 UTC  
> Updated at: 2017-08-29 08:51:25 UTC  
> Closed at: 2017-08-23 01:53:29 UTC  
> Url: https://github.com/boostorg/beast/issues/746  

Beast version 107  
  
I've chosen beast/example/advanced/server/advanced_server.cpp as the starting block for my project as I need to serve HTTP and websocket connections. I have noticed that the number of threads is being taken from argv[3] whereas I believe it should be argv[4].  
  
I have looked at a couple of the other examples and noticed the same error.  
  
While I'm here hope I could ask a quick question...  I am adding SSL support (I need an SSL-only server so I felt the server-flex example wasn't quite the right fit for me) so I was comparing with the http_server_async-ssl example. I noticed that it uses common/write_msg.hpp whereas advanced_server doesn't. Should it be? Is there any significance in that? Also the comments in write_msg describe how it uses async_write_some but I don't see it. I'm new to Asio and Beast so I might have misunderstood, but I don't see a composed operation, just a single call to boost::beast::http::async_write - apologies if I have misunderstood what's going on there!  
  
Thanks,  
Dave.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-18 13:51:55 UTC  
> Url: https://github.com/boostorg/beast/issues/746#issuecomment-323359407  

Looks like you found some bugs in the examples :) These will be fixed in version 108, which I will submit as a pull request shortly.  
  
The purpose of `async_write_msg` is to manage the lifetime of the outgoing HTTP response. It has to be valid for the duration of the async operation. But where would we store it? We can't make it a data member of the class because the outgoing response can have different types. For example `response<file_body>` and `response<string_body>`. So `async_write_msg` allocates storage to hold the response. `boost::http::async_write` is a composed operation which uses `async_write_some` under the hood. So the documentation for `async_write_msg` is accurate.  
  
The reason that advanced-server doesn't need `async_write_msg` is because it already has its own mechanism for storing type-erased response messages: the `queue` object which implements the pipelining.  
  
I will look at the other servers and see if maybe I can get rid of `async_write_msg`.

---

## Comment 2

> Username: dlaundon  
> Created at: 2017-08-18 14:15:53 UTC  
> Url: https://github.com/boostorg/beast/issues/746#issuecomment-323365547  

Thanks :)  
  
Ah I see... I will look again and make sure I understand what is going on.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-18 15:15:25 UTC  
> Url: https://github.com/boostorg/beast/issues/746#issuecomment-323381333  

I'd like your input on the changes: https://github.com/boostorg/beast/pull/747

---

## Comment 4

> Username: dlaundon  
> Created at: 2017-08-29 08:51:25 UTC  
> Url: https://github.com/boostorg/beast/issues/746#issuecomment-325598859  

Apologies, I've been away from the office. I'll catch up with the latest updates today!
