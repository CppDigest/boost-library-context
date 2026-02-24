# #1639 - strand/executor issue [Closed]

> Username: randomcoder91  
> Created at: 2019-06-23 03:36:23 UTC  
> Updated at: 2019-06-23 04:31:09 UTC  
> Closed at: 2019-06-23 04:30:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1639  

Hi,  
  
I used to create strands for my sockets like this.  
  
```  
class Test  
{  
public:  
	Test(const asio::ip::tcp::socket& socket) : strand(socket.get_io_service()) {}  
  
private:  
	asio::io_context::strand strand;  
}  
```  
  
With the latest version boost 1.70, how do I create a strand from the asio::ip::tcp::socket. ?  
The following doesn't seem to work.  
  
```  
class Test  
{  
public:  
	Test(const asio::ip::tcp::socket& socket) : strand(socket.get_executor().context()) {}  
  
private:  
	asio::io_context::strand strand;  
}  
```  
Thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-23 03:41:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1639#issuecomment-504716877  

```  
boost::asio::io_context ioc;  
boost::asio::tcp::socket sock(boost::asio::make_strand(ioc.get_executor()));  
```  
It is no longer necessary to maintain a separate `strand` object, or to use `bind_executor`.

---

## Comment 2

> Username: djarek  
> Created at: 2019-06-23 03:57:44 UTC  
> Updated at: 2019-06-23 03:58:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1639#issuecomment-504717523  

Note that the reason that `.get_executor().context()` doesn't work is that it doesn't necessarily return (in general, for some types it does) a type that satisfies the ExecutionContext concept (e.g. `asio::io_context` or `asio::thread_pool`).  
This function can also return `asio::execution_context&`, which, counterintuitively, does not satisfy the ExecutionContext concept.

---

## Comment 3

> Username: randomcoder91  
> Created at: 2019-06-23 04:05:19 UTC  
> Updated at: 2019-06-23 04:07:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1639#issuecomment-504717834  

Thank you both for your replies.

---

## Comment 4

> Username: randomcoder91  
> Created at: 2019-06-23 04:23:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1639#issuecomment-504718586  

I captured errors using the strand.wrap() handler. What is the way to do it without the strand ?  
  
`asio::async_write(socket, asio::buffer(buf, buflen), strand.wrap(...) { }`  
  
Is it like this ?  
  
`boost::asio::async_write(socket_, reply_.to_buffers(), boost::bind(&connection::handle_write, shared_from_this(), boost::asio::placeholders::error));`

---

## Comment 5

> Username: djarek  
> Created at: 2019-06-23 04:28:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1639#issuecomment-504718789  

`strand.wrap` has been deprecated for some time now. In order to avoid the need for `bind_executor`, you need to construct the socket using the strand:  
```  
asio::io_context ioc;  
asio::ip::tcp::socket s{asio::make_strand(ioc)};  
```

---

## Comment 6

> Username: randomcoder91  
> Created at: 2019-06-23 04:30:53 UTC  
> Updated at: 2019-06-23 04:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1639#issuecomment-504718877  

Thank you very much. I will try that.
