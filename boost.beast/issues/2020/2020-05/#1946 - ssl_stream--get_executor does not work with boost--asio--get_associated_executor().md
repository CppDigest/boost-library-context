# #1946 - ssl_stream::get_executor does not work with boost::asio::get_associated_executor() [Closed]

> Username: bgemmill  
> Created at: 2020-05-07 16:55:54 UTC  
> Updated at: 2020-05-07 20:05:29 UTC  
> Closed at: 2020-05-07 20:05:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1946  

### Version of Beast  
boost 1.73.0  
  
### Steps necessary to reproduce the problem  
```  
boost::beast::ssl_stream s(...);  
boost::asio::get_associated_executor(s);  
```  
It looks like  `boost::asio::get_associated_executor()` wants a const member function to work properly, but beast's `ssl_stream::get_executor()` is non-const.  
  
Would it be possible to include a const version, make the existing version const, or provide a specialization of get_associated_executor?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-07 17:21:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625389081  

@vinniefalco i seem to remember an outstanding question to Chris re the asio position on this?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-05-07 18:02:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625410042  

`get_associated_executor` operates on a completion token or completion handler, not a stream.

---

## Comment 3

> Username: bgemmill  
> Created at: 2020-05-07 19:08:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625442513  

@vinniefalco If I'm thinking about asio right, isn't stream the only one of those three that has an executor and requires it at construction time?  
  
If we consider:  
```  
boost::asio::io_context ioc;  
boost::asio::ssl::context ssl_ctx; // load with certs, etc...  
boost::beast::ssl_stream<boost::asio::ip::tcp::socket> socket{ioc, ssl_ctx};  
  
// connect to somewhere  
  
boost::asio::async_write(socket, "why hello there", [&](size_t sz auto ec){  
// do something async  
});  
ioc.run();  
```  
  
Given all the objects here, it seems like socket is the only one we'd expect boost::asio::get_associated_executor() to work on.  
  
Looking inside `boost/asio/ssl/stream.hpp`, it looks like boost::asio::ssl_stream marks this method as const.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-05-07 19:18:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625447078  

`get_associated_executor` is not meant to be called on streams. See 13.2.7.7 of https://cplusplus.github.io/networking-ts/draft.pdf

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-05-07 19:20:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625448147  

> Given all the objects here, it seems like socket is the only one we'd expect boost::asio::get_associated_executor() to work on.  
  
No, `get_associated_executor` is called on the lambda passed in the 3rd argument. And yes the socket is queried for its executor in the implementation, but not because it is passed as an argument to `get_associated_executor`. Note that asio's own SSL stream has a non-const `get_executor`:  
https://github.com/boostorg/asio/blob/d114cff2d264f38f7eafbdbb73a76707dbb99942/include/boost/asio/ssl/stream.hpp#L129  
  
This code is wrong:  
  
```  
boost::beast::ssl_stream s(...);  
boost::asio::get_associated_executor(s);  
```  
  
Don't do that.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-05-07 19:23:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625449477  

> i seem to remember an outstanding question to Chris re the asio position on this?  
  
This has nothing to do with that. `get_associated_executor` is not intended to be passed I/O objects.

---

## Comment 7

> Username: bgemmill  
> Created at: 2020-05-07 19:26:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625451091  

Looking at that spec, 13.2.7.8 (2.2) has:  
```  
If the initiating function is not a member function, the associated executor is that returned by the  
get_executor member function of the first argument to the initiating function.  
```  
  
If I'm looking at the free function async_write:  
```  
boost::asio::async_write(socket, "why hello there", [&](size_t sz, auto ec){/*do something*/});  
```  
  
Unless I'm really missing something, isn't stream.get_executor() what that section is referring to?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-05-07 19:28:59 UTC  
> Updated at: 2020-05-07 19:29:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625451945  

Yes but `stream.get_executor()` is called in the implementation of the composed operation, not by `get_associated_executor`. Do you expect the following code to compile?  
  
```  
boost::asio::ssl_stream< boost::asio::ip::tcp::socket > s( ioc );  
boost::asio::get_associated_executor( s );  
```  
  
Which part is unclear?

---

## Comment 9

> Username: bgemmill  
> Created at: 2020-05-07 20:05:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1946#issuecomment-625469185  

Honestly I did expect that to compile given the docs here:  
https://github.com/boostorg/asio/blob/d114cff2d264f38f7eafbdbb73a76707dbb99942/include/boost/asio/associated_executor.hpp#L60  
Where it's merely the association of an executor with an object.  
  
And here, where executor is singular rather than plural:  
https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/using_io/writing_composed_operations.html  
  
Reading those I'd assume that there was one executor all the way down the composed operation and that it could be acquired via `get_associated_executor`.   
  
`Which part is unclear?`  
Given that the spec mentions the first argument needs to have `.get_executor()` on it I'll just code for that and close the ticket.
