# #32 - double free on shutdown of awaited async_receive [Closed]

> Username: sgoth  
> Created at: 2025-05-21 10:39:35 UTC  
> Updated at: 2025-05-23 14:08:59 UTC  
> Closed at: 2025-05-23 14:06:38 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/32  

When aborting an awaited `async_receive()` through `async_disconnect()` i started to see a double free reported by GCC's (14 and 15) address sanitizer.  
  
I'm using `use_awaitable` throughout. The mqtt_client is used for both publish and receive.  
Everything works fine besides the asan error on shutdown.  
  
The receive loop spawned into the strand is:  
  
```cpp  
awaitable<void> RemoteService::mqttLoop()  
{  
	co_await subscribeSystems();  
//...  
  
	while (run_)  
	{  
		try  
		{  
			auto&& [topic, payload, publish_props] = co_await mqttClient_->async_receive(asio::use_awaitable);  
			  
			co_await dispatchIncomingMessage(topic, payload);  
		} // <<<--- this is line 307 in frame #10 of the asan report  
                catch(...) {  
                    // reduced here, actual handles all relevant exceptions  
                }  
         }  
}  
```    
  
When the application shuts down (i.e. SIGTERM) a normal sync function RemoteService::stop() tries to disconnect.  
It is called from a different io_context/executor. So it just co_spawns a coroutine that awaits the async_disconnect and waits on that through a future.  
  
async_disconnect never completes before the double free is raised.  
  
```cpp  
void RemoteService::stop() {  
	run_ = false;  
	scheduleTimer_.cancel();  
  
	if (mqttClient_)  
	{  
		std::promise<void> disconnect_promise;  
		auto disconnect_future = disconnect_promise.get_future();  
  
		co_spawn(  
			mqttStrand_,  
			[this, p = std::move(disconnect_promise)]() mutable -> asio::awaitable<void>  
			{  
				try  
				{  
					if (mqttClient_)  
					{  
						log()->trace("awaiting disconnect");  
						co_await mqttClient_->async_disconnect(mqtt5::disconnect_rc_e::normal_disconnection, mqtt5::disconnect_props{});  
						log()->trace("disconnected inside lambda");  
					}  
					p.set_value();  
				}  
				catch (...)  
				{  
					try  
					{  
						p.set_exception(std::current_exception());  
					}  
					catch (...)  
					{ /* std::promise::set_exception might throw */  
					}  
				}  
			},  
			asio::detached  
		);  
  
		try  
		{  
			log()->trace("waiting on future");  
			disconnect_future.get();   
		}  
		catch (const std::exception& e)  
		{  
			log()->warn("Failed to disconnect mqtt: {}", e.what());  
		}  
	}  
}  
```   
  
Enabling asio handler tracking shows the writing to the ssl socket (for disconnect), as well as the shutdown and then a bunch of ec=system:125 propagations for the cancellation.  
Seems all fine to me. Log from the point of stop() call: [asio handler trace](https://github.com/user-attachments/files/20365902/mqtt5-shutdown-handler-trace.txt)  
  
I started to notice this when switching from  
commit d52090f438906976c864698def2413b9d4ee097b to 82a437999a183fc3cf8a35e2aa96f6ea406d9b6f and it still happens as part of boost 1.88.0.  
  
[ASan report](https://github.com/user-attachments/files/20365913/mqtt5-doublefree.txt)  
  
To me it seems that the bound resources have been freed internally in the `async_receive` cancellation already when i have no other option than to free them myself too.  
Any ideas?

---

## Comment 1

> Username: sgoth  
> Created at: 2025-05-22 06:24:04 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/32#issuecomment-2900056557  

Just changing `async_receive` to `use_nothrow_awaitable` and handle returned ec instead of catching exceptions makes it work correctly without ASan error

---

## Comment 2

> Username: ksimicevic  
> Created at: 2025-05-22 11:31:39 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/32#issuecomment-2900883370  

Hello, and thank you for reporting this issue!  
  
We managed to reproduce the asan error on the g++ 15.1.1 compiler.  
However, the same example works on g++ 14.2.1, clang and msvc compilers.  
  
We're currently investigating why switching the `async_receive`'s completion token to `use_nothrow_awaitable` appears to resolve the double free.  
  
---  
  
Side note, I'd like to suggest that you can simplify your `RemoteService::stop()` function by using the `asio::use_future` completion token:  
  
```  
...  
  std::future<void> dc_future;  
  asio::dispatch(  
      mqttStrand_, [&] {  
          dc_future = mqttClient_.async_disconnect(asio::use_future);  
      }  
  );  
  dc_future.wait();  
...  
```  
  
This way you can avoid co_spawning the coroutine and manually fulfilling the `std::promise`.

---

## Comment 3

> Username: sgoth  
> Created at: 2025-05-22 12:31:26 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/32#issuecomment-2901058912  

> Hello, and thank you for reporting this issue!  
>   
> We managed to reproduce the asan error on the g++ 15.1.1 compiler. However, the same example works on g++ 14.2.1, clang and msvc compilers.  
>   
Great it's not only me holding things the wrong way :)  
  
I *thought* i noticed it with gcc 14 too, but now that you said that, i checked and internally documented the issue only after my system updated to gcc 15.1.1. So it's probably that!  
  
  
> ...   
  
> ```  
>   std::future<void> dc_future;  
>   asio::dispatch(  
>       mqttStrand_, [&] {  
>           dc_future = mqttClient_.async_disconnect(asio::use_future);  
>       }  
>   );  
>   dc_future.wait();  
> ```  
>   
> This way you can avoid co_spawning the coroutine and manually fulfilling the `std::promise`.  
  
That's great advice and so much nicer - thank you!

---

## Comment 4

> Username: biljazovic  
> Created at: 2025-05-23 14:06:38 UTC  
> Updated at: 2025-05-23 14:08:59 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/32#issuecomment-2904545508  

This seems to be caused by https://gcc.gnu.org/bugzilla/show_bug.cgi?id=120243
