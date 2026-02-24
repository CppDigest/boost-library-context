# #1615 - compile Error while upgrade from boost1.68 to boost1.70 [Closed]

> Username: opengpu  
> Created at: 2019-05-21 23:59:11 UTC  
> Updated at: 2019-10-14 19:31:53 UTC  
> Closed at: 2019-07-31 06:06:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1615  

error C2664: 'boost::asio::strand<boost::asio::io_context::executor_type>::strand(boost::asio::strand<boost::asio::io_context::executor_type> &&) noexcept': cannot convert argument 1 from 'boost::beast::websocket::stream<boost::asio::ip::tcp::socket,true>::executor_type' to 'const Executor &'  
1>        with  
1>        [  
1>            Executor=boost::asio::io_context::executor_type  
1>        ]  
  
note: Reason: cannot convert from 'boost::beast::websocket::stream<boost::asio::ip::tcp::socket,true>::executor_type' to 'const Executor'  
1>        with  
1>        [  
1>            Executor=boost::asio::io_context::executor_type  
1>        ]

---

## Comment 1

> Username: opengpu  
> Created at: 2019-05-22 00:02:17 UTC  
> Updated at: 2019-05-22 07:28:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-494602613  

class WebsocketSession : public std::enable_shared_from_this<WebsocketSession>  
{  
public:  
	// Take ownership of the socket  
	explicit WebsocketSession(boost::asio::ip::tcp::socket socket);  
	// Start the asynchronous operation  
	void run();  
	void on_accept(boost::system::error_code ec);  
	// Perform an asynchronous read operation.  
	void do_read();  
	void on_read(boost::system::error_code ec, std::size_t bytes_transferred);  
	// Perform an asynchronous write operation.  
	void do_write(const boost::beast::flat_buffer& flatBuf);  
	void on_write(boost::system::error_code ec, std::size_t bytes_transferred);  
protected:  
	boost::beast::websocket::stream<boost::asio::ip::tcp::socket> ws_;  
	boost::asio::strand<boost::asio::io_context::executor_type> strand_;  
	boost::beast::flat_buffer m_flatBufForRead; // (Must persist between reads)

---

## Comment 2

> Username: opengpu  
> Created at: 2019-05-22 00:02:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-494602750  

WebsocketSession::WebsocketSession(boost::asio::ip::tcp::socket socket)  
	: ws_(std::move(socket))  
	, strand_(ws_.get_executor())//!!!!here is the compile Error happened

---

## Comment 3

> Username: opengpu  
> Created at: 2019-05-22 00:05:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-494603211  

And one more question, is this Class good enough for Enterprise-use with a large number of users & high concurrency?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-05-22 00:39:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-494608991  

Please provide a MVCE:  
https://stackoverflow.com/help/reprex

---

## Comment 5

> Username: opengpu  
> Created at: 2019-05-22 07:29:06 UTC  
> Updated at: 2019-05-23 07:56:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-494686888  

, strand_(ws_.get_executor())//!!!!here is the compile Error happened  
  
boost::beast::websocket::streamboost::asio::ip::tcp::socket  ws_;

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-05-22 14:45:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-494834683  

See: https://github.com/boostorg/asio/commit/59066d80b26e1d5b83b60d127ee17948d9ae9702

---

## Comment 7

> Username: opengpu  
> Created at: 2019-05-23 07:57:07 UTC  
> Updated at: 2019-05-23 07:57:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-495109784  

so what should i use to replace "ws_.get_executor()"  
"ws_.get_executor().context()"?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-05-23 16:48:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-495297560  

Please provide a MVCE:  
https://stackoverflow.com/help/reprex

---

## Comment 9

> Username: stale[bot]  
> Created at: 2019-06-22 16:56:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-504681735  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 10

> Username: opengpu  
> Created at: 2019-06-24 02:24:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-504829618  

>   
>   
> This issue has been open for a while with no activity, has it been resolved?  
  
no

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-06-24 05:19:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-504861258  

Have you provided a minimal, complete, self-contained program that demonstrates the error?

---

## Comment 12

> Username: stale[bot]  
> Created at: 2019-07-24 05:27:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-514485100  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 13

> Username: stale[bot]  
> Created at: 2019-07-31 06:06:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-516709817  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 14

> Username: chreniuc  
> Created at: 2019-10-14 19:31:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1615#issuecomment-541875343  

@opengpu I had the same issue when I upgraded to 1.70.0, [here]'s my [post](https://github.com/boostorg/beast/issues/1122#issuecomment-473974440) and fix.
