# #47 - How to test for the client to be connected or not? [Open]

> Username: SiebrenW  
> Created at: 2026-01-27 09:25:23 UTC  
> Updated at: 2026-01-27 10:10:22 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/47  

I'm looking to use Boost::MQTT5 for my application, an embedded device communicating with other devices, but I kind of need to know (preferably also on a callback) whether the client has been connected/disconnected.  
  
My current application uses mosquitto and asio, making the interface between the two a common pain point. My abstraction was easy to convert, but the missing part seems to be the on_disconnect/on_connect callbacks.  
  
The application has different behaviour based on the connection (ie: stop sending more messages and create a safe state/recover)  
  
So is there such a thing, or should I keep looking?

---

## Comment 1

> Username: biljazovic  
> Created at: 2026-01-27 10:10:22 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/47#issuecomment-3804278749  

Connection state can be observed via a custom [logger](https://www.boost.org/doc/libs/latest/libs/mqtt5/doc/html/mqtt5/ref/LoggerType.html) object. On a successful connection, the `on_connack` callback is invoked. For disconnection, the `at_transport_error` callback can be used. For example, if no packets are received from the broker for `3 * keep_alive / 2` seconds, `at_transport_error` will be triggered with `boost::asio::error::timed_out`.  
  
See also https://github.com/boostorg/mqtt5/issues/45#issuecomment-3659839883
