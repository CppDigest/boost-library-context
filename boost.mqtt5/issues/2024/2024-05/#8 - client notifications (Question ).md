# #8 - client notifications  (Question ) [Closed]

> Username: amcharhal  
> Created at: 2024-05-13 10:47:20 UTC  
> Updated at: 2024-08-06 07:10:15 UTC  
> Closed at: 2024-08-06 07:10:15 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/8  

I'm unsure how to receive connection notifications from the client, such as when the client connects or disconnects, or encounters other errors like credential issues. In the simple example provided on the main page of https://github.com/mireo/async-mqtt5 (Usage and API), for instance, if the broker is inactive, the process remains pending without any notification

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-05-13 12:01:06 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/8#issuecomment-2107389401  

The key feature of our library is the auto-reconnect and retry mechanism, which means that the Client will repeatedly try to establish a connection to the Broker regardless of the reason why the connection failed (temporarily unavailable Broker, loss of connection, wrong credentials,...).  
  
As you have correctly observed, this means that if you provide the wrong credentials, the operation will keep trying to connect to the Broker without feedback. We covered this topic in [our documentation](https://spacetime.mireo.com/async-mqtt5/async_mqtt5/auto_reconnect.html) in which we describe our auto-reconnect and retry mechanism and limitations that come with this design choice.  
  
Currently, we do not have a logging mechanism that would notify you of such events. This is partly because we want easily customisable logging that provides zero overhead for users who do not need logging.  
  
However, you can still inject your own logging lines into the code.  
Specifically, in [connect_op.hpp](https://github.com/mireo/async-mqtt5/blob/master/include/async_mqtt5/impl/connect_op.hpp), I recommend logging in line 332, print the reason code (from the CONNACK packet) using `rc.message()` to inform you if you are using wrong MQTT configuration (credentials and connect properties).
