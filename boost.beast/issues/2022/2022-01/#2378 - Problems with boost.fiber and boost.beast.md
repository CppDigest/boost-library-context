# #2378 - Problems with boost.fiber and boost.beast [Closed]

> Username: zchGithub123  
> Created at: 2022-01-16 09:10:36 UTC  
> Updated at: 2022-06-14 17:38:37 UTC  
> Closed at: 2022-06-14 17:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2378  

I use boost.fiber to call the async_write of boost.beast's websocket, but Assertion failed: id_ != T::id occurs, which means that multiple identical asynchronous operations are not allowed, why, then fiber can't communicate with boost::beast is used together. sorry english is not my native language.My code is as follows:

---

## Comment 1

> Username: zchGithub123  
> Created at: 2022-01-16 09:10:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2378#issuecomment-1013838050  

void ws_connect::send(const std::string& strResponse)  
{  
	// auto spThis = shared_from_this();  
	// auto fnOnpost = [this, spThis, strResponse](){  
	// 	if (m_bThisValid)  
	// 	{  
	// 		m_queSendData.push(strResponse);  
	// 		1 == m_queSendData.size() ? do_send(m_queSendData.front()) : (void)0;  
	// 	}  
	// };  
  
	// m_spStrand4Send->post(fnOnpost, boost::asio::get_associated_allocator(fnOnpost));  
  
	//------------------------- 采用协程进行发送 ---------------------------------------------  
	m_spIO4Send->post([=](){  
		boost::fibers::fiber([=](){  
			boost::system::error_code ec;  
			m_spWsStream->async_write(boost::asio::buffer(strResponse, strResponse.size()), boost::fibers::asio::yield[ec]);  
			if (ec)  
			{  
				if (m_fnOnDetailMsg)  
					m_fnOnDetailMsg(ec.message());  
				if (m_fnOnErrorNotify)  
					m_fnOnErrorNotify(m_uID);  
				return;  
			}  
		}).detach();  
	});  
}

---

## Comment 2

> Username: zchGithub123  
> Created at: 2022-01-16 09:24:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2378#issuecomment-1013840124  

This block of code will always be called as long as there is data to be pushed out

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-01-16 10:32:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2378#issuecomment-1013849989  

You will need to ensure that there are no concurrent writes to the stream. The usual way to do with is with either a queue or an asynchronous semaphore.  
  
https://github.com/madmongo1/asio_experiments/blob/7ec255386aba36202571024439b1ef23614cd301/include/asioex/async_semaphore.hpp#L63

---

## Comment 4

> Username: zchGithub123  
> Created at: 2022-01-16 10:38:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2378#issuecomment-1013850945  

I use boost.fiber to achieve concurrency, Boost.fiber provides a framework for micro-/userland-threads (fibers) scheduled cooperatively. The API contains classes and functions to manage and synchronize fibers similar to boost.thread.

---

## Comment 5

> Username: zchGithub123  
> Created at: 2022-01-16 10:41:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2378#issuecomment-1013851356  

Where is an asynchronous semaphore use case

---

## Comment 6

> Username: zchGithub123  
> Created at: 2022-01-16 10:42:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2378#issuecomment-1013851502  

thanks for answer

---

## Comment 7

> Username: madmongo1  
> Created at: 2022-01-16 10:56:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2378#issuecomment-1013853445  

https://github.com/madmongo1/boost_connector/blob/1d1b02f168bba88778b4c1154058bd228389895a/src/vendor/ftx/ftx_websocket_connector_concept.cpp#L73  
  
In this example, the `send` method pushes messages onto an `async_queue`. One coroutine (or fiber) waits for messages to appear in the `async_queue` before initiating an `async_send`. It does not await the `async_queue` until the `async_send` operation has completed.  
  
In my example, here is the coroutine doing this:  
https://github.com/madmongo1/boost_connector/blob/1d1b02f168bba88778b4c1154058bd228389895a/src/vendor/ftx/ftx_websocket_connector_concept.cpp#L124

---

## Comment 8

> Username: zchGithub123  
> Created at: 2022-01-16 11:04:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2378#issuecomment-1013854785  

ok, Thank you very much, I will study it. looks difficult.
