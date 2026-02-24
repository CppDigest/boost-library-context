# #1868 - [Question]: timeout behaviour in `http_server_async.cpp` [Closed]

> Username: jcmonnin  
> Created at: 2020-03-02 02:26:13 UTC  
> Updated at: 2020-03-04 02:15:01 UTC  
> Closed at: 2020-03-04 02:15:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1868  

As a reminder, here is the method `do_read()` method from the sample:  
  
    void  
    do_read()  
    {  
        // Make the request empty before reading,  
        // otherwise the operation behavior is undefined.  
        req_ = {};  
  
        // Set the timeout.  
        stream_.expires_after(std::chrono::seconds(30));  
  
        // Read a request  
        http::async_read(stream_, buffer_, req_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
It sets a fixed timeout when it starts to wait for the request. With this approach, I get into following issues:  
  
1. browsers open a couple of connections to the server and reuse the connections for multiple requests. If a a `XMLHttpRequest` is sent with the bad timing, it will fail (eg. a 2 second download started 29 seconds after the connection has been idle).  
2. If the request takes longer than the timeout to process it is aborted (eg. big file downloads have no chance to go through).  
  
I'm hitting issue (1) regularly on a web app that loads big resources on demand.  
  
I'd like to have a timeout on true idle activity. It should timeout if nothing is sent over the connection for 30 seconds (in contrast to 30 seconds since last call to `do_read`). I don't see a clean way to achieve that with a `tcp_stream`. Any advice?  
  
Note that I'm aware beast is a low level library and that the samples are not meant to be production ready server. Any pointer to a more complete http server implementation using beast is also welcome.  
  
Thanks.  
  
Edit: Adding a ` stream_.expires_after(std::chrono::seconds(30));` before `http::async_write` is my current "fix" for the issue, however it doesn't change the timeout semantics.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-02 14:17:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1868#issuecomment-593423617  

I'll defer to @vinniefalco for advice here.  
  
When I wrote a web server with Beast, I used a standard stream and set up my own timeouts (using asio::system_timer) on the various phases of waiting for a request and receiving sections of the body.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-03-02 16:09:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1868#issuecomment-593479884  

>If the request takes longer than the timeout to process it is aborted (eg. big file downloads have no chance to go through).  
  
Use `http::async_read_some` in a loop.

---

## Comment 3

> Username: jcmonnin  
> Created at: 2020-03-02 22:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1868#issuecomment-593649095  

Thanks. It looks like I have to break down the composed operations `http::async_read` and `http::async_write` using `http::async_read_some` and `http::async_write_some` to allow to push the deadline further back as long as data arrives or is sent.  
  
I tried to follow the implementation of `http::async_read` but was quickly lost in the abstraction layers. I could not find a sample using `http::async_read_some`. Anyway, I guess it's not that complicated and I just need to do the homework of learning more about the http part of beast.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-03-02 22:10:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1868#issuecomment-593649401  

Alternatively you could just implement your own timer. You don't have to use the one in Beast.

---

## Comment 5

> Username: jcmonnin  
> Created at: 2020-03-02 22:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1868#issuecomment-593650827  

Yes, but I will still need to use `http::async_write_some` instead of `http::async_write` to push my own timer back if data is still sent. I'm just struggling to write my own loop with `http::async_write_some` (because I haven't spent enough time understanding beast http interfaces).

---

## Comment 6

> Username: djarek  
> Created at: 2020-03-02 22:20:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1868#issuecomment-593653590  

@jcmonnin If you're dealing with large uploads/downloads, you'll need to manage 2 deadlines - the short inactivity one (time since last transfer) and a long, per request one. Alternatively, you can also set a minimal transfer rate.  
  
You need this in order to prevent slow-loris-like DoS attacks, where an attacker starts multiple requests and sends 1 byte at a time, hogging a lot of resources in the server.

---

## Comment 7

> Username: jcmonnin  
> Created at: 2020-03-04 02:15:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1868#issuecomment-594280551  

Thanks for your advice
