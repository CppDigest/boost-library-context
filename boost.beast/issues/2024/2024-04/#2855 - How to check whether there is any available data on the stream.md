# #2855 - How to check whether there is any available data on the stream [Closed]

> Username: zhouxindong  
> Created at: 2024-04-19 14:25:33 UTC  
> Updated at: 2024-04-22 02:03:59 UTC  
> Closed at: 2024-04-22 02:03:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2855  

I have established a websocket connection and need to periodically check whether there is available data on the connection, read it if there is, and return immediately if not.   
I used async_read_some(), the completion handler was called after a few seconds and the data was read, but this did not meet my needs. How can I detect if data is available on the connection at a certain moment?  
Thanks a lot!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-04-19 14:35:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2855#issuecomment-2066717178  

That's not how it is done. Instead you perform an async read and then when there is a data, or a complete message, your completion handler is invoked. Why do you want to "detect if data is available?"

---

## Comment 2

> Username: ashtum  
> Created at: 2024-04-19 15:06:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2855#issuecomment-2066773046  

@zhouxindong Typically, we structure our applications around Asio's executor, ensuring that everything operates through asynchronous events. In situations where we must wait for multiple events, we have the option to employ [experimental::make_parallel_group](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/experimental__make_parallel_group.html) or to handle the state manually. Otherwise, synchronization between multiple threads can become a nightmare.  
  
If you're confident about your try-to-read design, you can simply utilize the [asio::use_future](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/use_future.html) completion token and query the state of the returned future:  
  
```C++  
auto fut = stream.async_read(buf, asio::use_future);  
  
// inside your loop  
if (fut.wait_for(std::chrono::seconds{ 0 }) == std::future_status::ready)  
{  
  //  
}  
```  
Note: if you are running the io_context in a separate thread, you need to make sure your Websocket object is only accessed from one thread.

---

## Comment 3

> Username: zhouxindong  
> Created at: 2024-04-20 01:22:05 UTC  
> Updated at: 2024-04-20 01:27:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2855#issuecomment-2067499372  

> That's not how it is done. Instead you perform an async read and then when there is a data, or a complete message, your completion handler is invoked. Why do you want to "detect if data is available?"  
  
Thank you very much for your reply!  
My needs are:  
1. Periodically drain the data on the connection (such as heartbeat data)  
2. Data will be sent from time to time and then a response will be received. The response data cannot be read by op1.  
op1 will be temporarily disabled before op2 starts, but it is possible that async read of op1 is still working.

---

## Comment 4

> Username: zhouxindong  
> Created at: 2024-04-20 01:24:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2855#issuecomment-2067500101  

> @zhouxindong Typically, we structure our applications around Asio's executor, ensuring that everything operates through asynchronous events. In situations where we must wait for multiple events, we have the option to employ [experimental::make_parallel_group](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/experimental__make_parallel_group.html) or to handle the state manually. Otherwise, synchronization between multiple threads can become a nightmare.  
>   
Thank you a lot!  
I will try it. Thanks again!
