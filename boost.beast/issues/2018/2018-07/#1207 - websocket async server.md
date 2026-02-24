# #1207 - websocket async server [Closed]

> Username: yongs2018  
> Created at: 2018-07-23 16:31:19 UTC  
> Updated at: 2018-08-31 15:16:13 UTC  
> Closed at: 2018-08-31 15:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1207  

when I send buffer   
  
Assertion failed: ! base_, file e:\sdkroot\boost_x64\include\boost-1_66\boost\beast\websocket\detail\pausation.hpp, line 213

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-23 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-407121067  

When you call `websocket::stream::async_write`, you must wait until the operation completes (your completion handler is invoked) before calling `async_write` again. This is explained here:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety  
  
If you want to send more than one message at a time, you need to implement your own write queue. An example may be found here:  
https://github.com/vinniefalco/CppCon2018/blob/8fc8528571561d2acee5c05f4b7a51861d1d496d/websocket_session.cpp#L87

---

## Comment 2

> Username: yongs2018  
> Created at: 2018-07-23 16:51:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-407125751  

thank you , super man .  Your eyes are like Chairman Mao

---

## Comment 3

> Username: yongs2018  
> Created at: 2018-07-23 17:55:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-407145748  

this code Cause the same problem：   
bool Msession::send_msg(const std::string & msg)  
{  
	bool write_pending = false;  
	{  
		std::lock_guard<std::mutex> lock(mtx_);  
		write_pending = !m_sendQueue.empty();  
		m_sendQueue.emplace_back(msg);  
	}  
  
	if (!write_pending)  
	{  
		async_write();  
	}  
	return true;  
}  
  
void Msession::async_write()  
{  
	std::string msg;  
	{  
		std::lock_guard<std::mutex> lock(mtx_);  
		if (m_sendQueue.empty())  
			return;  
		msg = m_sendQueue.front();  
	}  
	auto sp = std::make_shared<std::string>(msg);  
	//auto b = boost::asio::dynamic_buffer(*sp);  
	  
	ws_.async_write(  
			boost::asio::buffer(*sp), boost::asio::bind_executor(strand_,  
				[sp, self = shared_from_this()](boost::beast::error_code ec, std::size_t bytes_transferred)  
		{  
			self->on_write(ec, bytes_transferred);  
		}));  
}  
  
void  
Msession::on_write(  
	boost::system::error_code ec,  
	std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	if (ec)//error  
	{  
  
		std::lock_guard<std::mutex> lock(mtx_);  
		m_sendQueue.clear();  
		SendBuffer* sq = SendBuffer::getInstance();  
		auto p = shared_from_this();  
		sq->del_fd(p);  
		std::cout << "write close" << std::endl;  
		return;  
	}  
  
	if (!ec)  
	{  
		std::lock_guard<std::mutex> lock(mtx_);  
		std::cout << "m_sendQueue.size()" << m_sendQueue.size() << std::endl;  
		m_sendQueue.pop_front();  
  
	}  
	async_write();  
}  
  
Assertion failed: ! base_, file e:\sdkroot\boost_x64\include\boost-1_66\boost\beast\websocket\detail\pausation.hpp, line 213

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-07-23 18:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-407150285  

I would try using a more recent Boost, 1.66.0 has a few websocket bugs that were fixed in later versions

---

## Comment 5

> Username: yongs2018  
> Created at: 2018-07-24 08:38:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-407328078  

1.68 version ok ?  for this bug .

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-07-24 12:44:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-407393580  

Yes

---

## Comment 7

> Username: yongs2018  
> Created at: 2018-07-25 07:09:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-407656479  

Assertion failed: id_ != T::id, boost\beast\websocket\detail\stream_base.hpp, line 91  ？

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-07-25 13:53:45 UTC  
> Updated at: 2018-07-25 13:54:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-407761966  

If you are getting assertions, it almost certainly means that your code is violating one or more preconditions. This is often the result of accessing I/O objects and the classes that access them (like `websocket::stream`) in ways that are disallowed by the interface. For example, by omitting the strand when multiple threads are calling `io_service::run`.  
  
I can't really help without a clearly articulated statement of the problem, so my general advice is as follows: start by compiling one of the examples which operates similarly to your desired use-case, to confirm that it works (they do). Then modify the example in small increments to achieve the required functionality. Along the way, if the program malfunctions, then it is highly likely that your last change is the culprit.

---

## Comment 9

> Username: stale[bot]  
> Created at: 2018-08-24 14:17:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-415772621  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 10

> Username: stale[bot]  
> Created at: 2018-08-31 15:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1207#issuecomment-417696569  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
