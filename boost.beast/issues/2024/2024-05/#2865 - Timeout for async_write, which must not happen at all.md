# #2865 - Timeout for async_write, which must not happen at all [Closed]

> Username: SoulfreezerXP  
> Created at: 2024-05-12 12:49:26 UTC  
> Updated at: 2024-07-04 16:39:12 UTC  
> Closed at: 2024-07-04 16:39:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2865  

I think I have found a (adjustable!) bug where the timout of async_write() simply triggers, although it is not allowed to trigger!  
I have only used the original example from [boost.org](https://www.boost.org/doc/libs/1_85_0/libs/beast/example/advanced/server/advanced_server.cpp) slightly slimmed down.   
  
If I now run this server (compiled with C++20, GCC13 under Linux Ubuntu 13.2.0 (other systems are also affected)  
and then I use a http-client (uses http-pipelining + keepalive) that I wrote in go, I get a WRITE timeout  
after firing MANY requests!  
  
However, this may not appear here at all, because I call the "stream_.expires_after" before each asyn_read() call.  
There are no visual delays and the TCP buffers are not full and according to the documentation,  
the timeout should also apply to async_write. The system is underutilized, which means   
that no timout may occur anywhere  
  
**You can find the server here:**  
https://coliru.stacked-crooked.com/a/bb251d267573feac  
Start it with:  ./beast_server_example 0.0.0.0 8080 4   
  
**The go client is here (build with: "go build ."):**    
https://coliru.stacked-crooked.com/a/1537cffa5303fb46  
Start it with (wait 30-60 seconds):  ./http_client / 10000000  
  
**I have also discovered a workaround.**   
When I add "stream_.expires_after(std::chrono::seconds(30))" ALSO before the async_write()-method, then I can send an   
infinite number of requests. There is never a timout, not even after millions of requests.   
So there is something unintuitive going on here. Maybe the problem here is that the timeout for async_write is simply not reset with every new async_read?  
  
Check also the Docu:   
[expires_after](https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream/expires_after.html)  
=> // Require that the read and write COMBINED take no longer than 30 seconds  
=> stream.expires_after(std::chrono::seconds(30));

---

## Comment 1

> Username: ashtum  
> Created at: 2024-05-12 16:00:13 UTC  
> Updated at: 2024-05-12 16:21:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2865#issuecomment-2106299369  

Calling [basic_stream::expires_after](https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream/expires_after.html) while there is a pending write operation, does not update the write timer:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/impl/basic_stream.hpp#L814  
  
Because of `response_queue_` every time you call `expires_after` (except the first time) there is a pending write operation which prevents updating the write timer.  
  
If you change `queue_limit` to 1, you will observe that the timeout does not occur. An easy fix would be to call `expires_after` before initiating the write operation, as that is the only place where you can be confident that there is no pending write operation.  
  
By the way, I believe we should update the documentation for `basic_stream::expires_after` and `basic_stream::expires_at` to mention this behavior.

---

## Comment 2

> Username: SoulfreezerXP  
> Created at: 2024-05-12 16:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2865#issuecomment-2106305349  

For me it is surprising from the user's point of view, because a timeout simply occurs after a certain time, even though there are ALWAYS read and write operations. There is no period of time in which nothing happens, so that a timeout for anything at all would be justified.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-05-12 16:22:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2865#issuecomment-2106305659  

An easy fix would be to call `expires_after` before initiating the write operation, as that is the only place where you can be confident that there is no pending write operation.

---

## Comment 4

> Username: SoulfreezerXP  
> Created at: 2024-05-12 16:23:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2865#issuecomment-2106306069  

I have already mentioned this in my request. But this is more of a workaround for me.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-05-12 16:38:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2865#issuecomment-2106309594  

The reason you can't update the write timer while it is pending is because it has already initiated an `async_wait` operation on that timer (with whatever timeout value was set). Therefore, updating it would be logically incorrect and more surprising for users.  
  
Could you please provide more context or clarify the specific logic or behavior you want to implement? You might consider updating a time point inside `on_read` operation an use [basic_stream::expires_at](https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream/expires_at.html) interface with the time offset you need before calling `async_write`.

---

## Comment 6

> Username: SoulfreezerXP  
> Created at: 2024-05-12 17:14:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2865#issuecomment-2106318335  

I just wanted to _report_ this behavior, because according to the documentation it sounded to me like the timer should have been reset automatically with the next async_read(...). It was not clear to me that the write timeout would not be reset.  
  
"The timer applies collectively to any asynchronous reads or writes initiated after the expiration is set, **until the expiration is set again**"

---

## Comment 7

> Username: SoulfreezerXP  
> Created at: 2024-05-12 18:35:39 UTC  
> Updated at: 2024-05-12 21:42:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2865#issuecomment-2106339093  

Maybe each async method could **pull** the last set expiry time, when it was not possible to set it before because of pending?  
Here is some pseudo code (mutexes may or may not also be required) for the idea...unfortunately I hardly know boost:  
  
```  
std::atomic_bool read_expiry_time_set_is_pending{ false };  
std::atomic_bool write_expiry_time_set_is_pending{ false };  
std::atomic< net::steady_timer::duration > lastSetExpiryTime;  
  
  
basic_stream<Protocol, Executor, RatePolicy>::  
expires_after(net::steady_timer::duration expiry_time)  
{  
	//Add this at top  
	lastSetExpiryTime.store( expiry_time );  
	read_expiry_time_set_is_pending  = impl_->read.pending;  
	write_expiry_time_set_is_pending = impl_->write.pending;  
  
	...	  
	...	  
}  
  
  
async_read( ... )  
{  
	//Add this at top  
	if (read_expiry_time_set_is_pending)  
	{  
		read_expiry_time_set_is_pending = false;  
		BOOST_VERIFY(impl_->read.timer.expires_after(lastSetExpiryTime.load()) == 0);  
	}  
	  
	...  
	...  
}  
  
async_write( ... )  
{  
	//Add this at top  
	if (write_expiry_time_set_is_pending)  
	{  
		write_expiry_time_set_is_pending = false;		  
		BOOST_VERIFY(impl_->write.timer.expires_after(lastSetExpiryTime.load()) == 0);  
	}  
	  
	...  
	...  
}  
```
