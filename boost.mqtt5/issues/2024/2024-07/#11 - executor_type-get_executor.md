# #11 - executor_type/get_executor [Closed]

> Username: klemens-morgenstern  
> Created at: 2024-07-27 00:50:02 UTC  
> Updated at: 2024-08-09 07:44:51 UTC  
> Closed at: 2024-08-09 07:44:50 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/11  

With boost 1.86 some tokens (namely `cancel_at` and `cancel_after`) and the partial tokens use the `associated_executor` from the initiation object. This allows the following:  
  
```cpp  
c.async_publish<async_mqtt5::qos_e::at_most_once>(  
		"<topic>", "Hello world!",  
		async_mqtt5::retain_e::no, async_mqtt5::publish_props {},  
		asio::cancel_after([&c](async_mqtt5::error_code ec) {  
			std::cout << ec.message() << std::endl;  
			c.async_disconnect(boost::asio::detached); // disconnect and close the client  
		}, std::chrono::milliseconds(100)));  
	);  
```  
  
it will also allow partial tokens, e.g. the following:  
  
  
```cpp  
auto [ec] = co_await c.async_publish<async_mqtt5::qos_e::at_most_once>(  
		"<topic>", "Hello world!",  
		async_mqtt5::retain_e::no, async_mqtt5::publish_props {}, asio::as_tuple);  
  
std::cout << ec.message() << std::endl;  
```

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-08-09 07:44:50 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/11#issuecomment-2277357084  

Resolved with 40b1f7e00cb861a5b8d6b97dda148ed965792b19.
