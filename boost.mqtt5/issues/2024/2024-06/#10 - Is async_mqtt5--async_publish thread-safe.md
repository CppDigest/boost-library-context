# #10 - Is async_mqtt5::async_publish thread-safe ? [Closed]

> Username: amarmemic  
> Created at: 2024-06-14 09:14:28 UTC  
> Updated at: 2024-08-06 07:09:57 UTC  
> Closed at: 2024-08-06 07:09:57 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/10  

Hi !   
  
When I call async_publish, the method below is invoked. Is this correct?   
Is it thread-safe to use more than one thread for the I/O context?  
  
https://github.com/mireo/async-mqtt5/blob/d52090f438906976c864698def2413b9d4ee097b/include/async_mqtt5/impl/async_sender.hpp#L228-L290  
  
Pozdrav :) ,   
  
Amar

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-06-14 12:20:33 UTC  
> Updated at: 2024-06-14 12:45:17 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/10#issuecomment-2167907853  

Hi,  
  
Asio-compliant objects (like our `mqtt_client`) and their member functions (like `async_publish`) are not thread-safe. This is intentional to avoid the overhead associated with mutexes and other synchronisation mechanisms in single-threaded applications.  
  
Regardless, you can use more than one thread to run `io_context`. In that case,  you should make sure that the `mqtt_client`'s functions are executed within the [`strand`](https://www.boost.org/doc/libs/1_82_0/doc/html/boost_asio/reference/io_context__strand.html) (an executor that guarantees that tasks assigned to it are executed in a serialised manner, preventing concurrent execution).   
  
You can achieve that by:  
  
1.  constructing the `mqtt_client` with a `strand` object as its executor  
2.  dispatching all `mqtt_client`'s function calls to that `strand` using `post`  or `dispatch`   
3.  ensuring that the `async_xxx`'s completion handler's associated executor is that same `strand`  
  
This is how you would call `async_publish` in a multi-threaded environment:  
  
	  boost::asio::strand strand = boost::asio::make_strand(ioc.get_executor());  
	  async_mqtt5::mqtt_client<boost::asio::ip::tcp::socket> client(strand);  
	  client.brokers("<your-mqtt-broker>", 1883);  
	  boost::asio::post(  
		  strand,  
		  [&client, &strand] {  
			// Considering that the default associated executor of all completion handlers is the strand,  
			// it is not necessary to explicitly bind it to async_run or other async_xxx's handlers.  
			  client.async_run(boost::asio::detached);  
		  }  
	  );  
	  boost::asio::post(  
		  strand,  
		  [&client, &strand] {  
			  client.async_publish<async_mqtt5::qos_e::at_least_once>(  
				  "<your-mqtt-topic>", "Hello world!", async_mqtt5::retain_e::no,  
				  async_mqtt5::publish_props {},  
				  [&client, &strand](  
					  async_mqtt5::error_code ec, async_mqtt5::reason_code rc,  
					  async_mqtt5::puback_props props  
				  ) {  
				  }  
			  );  
		  }  
	  );  
	    
Now, you can freely use as many threads as you'd like for your `io_context` and not worry about any concurrency issues.  
  
Additionally, we have thoroughly covered this topic in [our documentation](https://spacetime.mireo.com/async-mqtt5/async_mqtt5/multithreading.html). I'd recommend giving it a glance.  
  
Hope this helps!
