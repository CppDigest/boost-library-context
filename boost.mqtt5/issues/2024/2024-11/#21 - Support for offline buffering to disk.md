# #21 - Support for offline buffering to disk [Closed]

> Username: dmf254  
> Created at: 2024-11-04 20:00:10 UTC  
> Updated at: 2024-11-06 16:18:35 UTC  
> Closed at: 2024-11-06 16:18:35 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/21  

Does this library provide any support or customization points for persisting messages to disk while offline?

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-11-05 13:27:40 UTC  
> Updated at: 2024-11-05 13:28:53 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/21#issuecomment-2457184919  

The client does not explicitly provide customization points for persisting messages. This is intentional, as it gives users more flexibility in implementing their persistence strategy, and avoids the complexity of defining non-trivial interfaces and dealing with type-erasure that comes with it. Note that the Broker is not required to acknowledge messages in the same order it receives them. Theoretically, a later message could complete transmission before an earlier one.  
  
However, it should be relatively straightforward to implement your persistence mechanism. Simply persist the message before calling `async_publish` and pop the message after its handler is invoked, as shown below:  
```  
auto msg = generate_message();  
persist_message(msg); // persist to storage  
  
// Example: publish the message at QoS 1 (same logic applies to both QoS 0 and QoS 2)  
client.async_publish<async_mqtt5::qos_e::at_least_once>(  
    topic, msg, retain, props,  
    [&msg](async_mqtt5::error_code ec, async_mqtt5::puback_props) {  
        if (!ec) // Message was successfully sent and acknowledged by the Broker if QoS >0  
            pop_message(msg); // Safe to remove message from persistent storage  
        else  
            // Handle errors  
    }  
);  
```  
Or if you are using coroutines:  
```  
auto msg = generate_message();  
persist_message(msg); // persist to storage  
auto [ec, props] = co_await client.async_publish<async_mqtt5::qos_e::at_least_once>(topic, msg, retain, props, asio::as_tuple(asio::deferred));  
if (!ec) // Message was successfully sent and acknowledged by the Broker if QoS >0  
    pop_message(msg); // Safe to remove message from persistent storage  
else   
    // Handle errors  
```  
  
Note that the client buffers outgoing requests in memory when offline, but this buffer is limited due to the requirement for a unique Packet Identifier for each `async_publish` (QoS > 0), `async_subscribe`, and `async_unsubscribe` request (PIDs 1-65535). If the client remains offline for an extended period and keeps publishing, it may reach this limit. Any `async_publish (QoS > 0)`, `async_subscribe`, or `async_unsubscribe` operation after the limit will complete immediately with `async_mqtt5::client::error::pid_overrun`. That case should be handled accordingly.

---

## Comment 2

> Username: dmf254  
> Created at: 2024-11-06 16:18:16 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/21#issuecomment-2460215847  

Thanks for the quick answer, I'll try a persistence implementation similar to what you described.
