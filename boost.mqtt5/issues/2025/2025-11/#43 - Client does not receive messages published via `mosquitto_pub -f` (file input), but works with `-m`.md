# #43 - Client does not receive messages published via `mosquitto_pub -f` (file input), but works with `-m` [Closed]

> Username: Mq-b  
> Created at: 2025-11-20 02:51:29 UTC  
> Updated at: 2025-12-10 09:25:06 UTC  
> Closed at: 2025-12-10 09:25:06 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/43  

Hello, thank you for developing and maintaining this excellent MQTT5 library.  
  
I'm using your library to write an MQTT client that subscribes to messages. My setup involves a local Mosquitto broker and the `mosquitto_pub` tool for testing.  
  
**I've encountered an issue where my client successfully receives messages published using both the `-m` option (e.g., `mosquitto_pub -t "test/topic" -m "Hello"`) and the `-f` option with regular text files. However, when I publish binary files (such as JPG images, approximately less than 1MB in size) using the `-f` option (e.g., `mosquitto_pub -t "test/topic" -f image.jpg`), my client does not receive the message. There are no errors logged on the client side; it simply doesn't detect the incoming publication, and the message appears to be completely ignored.**  
  
**Code Reference**  
My client code and project structure can be found here:  
[https://github.com/Mq-b/Lab2QRCode/tree/master/src/mqtt](https://github.com/Mq-b/Lab2QRCode/tree/master/src/mqtt)  
  
(The relevant subscriber logic is primarily in the `MqttClient` class).  
  
**Additional Context**  
- I understand that my client code might be incomplete or have bugs, and I am seeking guidance.  
- The broker is the standard Eclipse Mosquitto broker.  
- I am using the [`mosquitto_pub`](https://mosquitto.org/man/mosquitto_pub-1.html) tool that comes with the [Mosquitto](https://github.com/eclipse-mosquitto/mosquitto) installation.  
  
My code is quite simple, consisting of just one header file and one CPP file. If you have time, I would greatly appreciate it if you could take a look and provide some guidance. I'm unsure why I can't receive binary files—the message simply never arrives. Additionally, I'd like to ask where I should set the QoS level. While I've seen the relevant enumeration, I haven't found where to actually configure it.  
  
Any insight or suggestions would be greatly appreciated. Thank you for your time and support.

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-11-20 10:31:12 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/43#issuecomment-3557155126  

Hi,  
  
By default, maximum packet size client allows is 64KB, and client advertises this limit to the broker so the broker will not attempt to send larger publish packets. The limit could definitely be better documented, it's only mentioned in the note [here](https://www.boost.org/doc/libs/latest/libs/mqtt5/doc/html/mqtt5/getting_started.html#mqtt5.getting_started.configuration).  
  
You can increase the limit by setting the connect property `maximum_packet_size` as follows:  
```cpp  
client  
  .brokers(..).credentials(..)  
  .connect_property(boost::mqtt5::prop::maximum_packet_size, 1024 * 1024) // 1MB  
  .async_run(..)  
```  
  
To set the Publish QoS level, pass the desired QoS as a template parameter of the `async_publish` function. Here's the example of setting QoS 1: https://github.com/boostorg/mqtt5/blob/6198b0e44a955563e49545564b2a1bf3dab58062/example/publisher.cpp#L67-L70

---

## Comment 2

> Username: Mq-b  
> Created at: 2025-11-20 11:25:27 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/43#issuecomment-3557477786  

Thanks!

---

## Comment 3

> Username: Mq-b  
> Created at: 2025-11-21 07:56:04 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/43#issuecomment-3561857612  

# Inquiry about MQTT Persistent Message Configuration  
  
Dear Author,  
  
Thank you very much for your excellent library – it's incredibly useful and well-designed.  
  
I have a question regarding its usage, specifically about MQTT's persistent messaging. Based on my understanding of the MQTT protocol, it should support a scenario where messages published to a topic are retained by the broker and delivered to subscribers even if they were offline at the time of publication.  
  
For our implementation using the MQTT5 library, what is the recommended configuration to achieve this reliably? I understand that we need to use a persistent connection and set the QoS to at least 1. Beyond that, are the message storage and delivery guarantees primarily handled by the broker itself, or are there specific client-side settings we must apply?  
  
My goal is to leverage MQTT for two critical functions:  
1. **Remote Updates**: Ensuring devices receive update commands as soon as they come online.  
2. **Critical Messaging**: Publishing important messages that must not be missed and should ideally be processed only once.  
  
Any guidance or best practices you could share would be greatly appreciated.  
  
Thank you for your time and for creating such a valuable tool.  
  
Best regards,  
  
Mq-b

---

## Comment 4

> Username: biljazovic  
> Created at: 2025-11-21 10:08:36 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/43#issuecomment-3562313719  

Thanks for the question, here's how to configure it reliably:  
  
On the receiving side:  
  * Subscribe with `qos_e::exactly_once` (this is the default).  
  * Configure a non-zero `session_expiry_interval`, for example:  
  ```cpp  
    client.connect_property(boost::mqtt5::prop::session_expiry_interval, 60)  
  ```  
  * If `async_receive` completes with error code `boost::mqtt5::error::session_expired`, re-subscribe. See the example here: https://github.com/boostorg/mqtt5/blob/6198b0e44a955563e49545564b2a1bf3dab58062/example/receiver.cpp#L91-L108  
  
On the publishing side:  
  * Publish with `qos_e::exactly_once`.  
  * Optionally, if you expect the receiver to be offline longer than its `session_expiry_interval`, set the retain flag to true, so the broker stores the last message for newly reconnected subscribers.

---

## Comment 5

> Username: Mq-b  
> Created at: 2025-11-21 10:28:05 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/43#issuecomment-3562387696  

Thanks.

---

## Comment 6

> Username: Mq-b  
> Created at: 2025-11-21 10:36:26 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/43#issuecomment-3562422803  

I understand now. The idea is that the subscriber can use the default `qos_e::exactly_once` without worrying about it, and just configure the `session_expiry_interval`.  
  
On the publisher side, you need to make sure to also publish with `qos_e::exactly_once`, and remember to set the `retain` flag to `true`.
