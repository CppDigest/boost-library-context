# #17 - Slow performance on localhost + Windows [Closed]

> Username: Faaux  
> Created at: 2024-10-18 11:41:40 UTC  
> Updated at: 2024-10-18 12:22:58 UTC  
> Closed at: 2024-10-18 12:22:58 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/17  

Hey there,  
  
I am currently hunting for a MQTT OSS Library and also checked out async-mqtt5. Currently I am using paho-mqtt-cpp which has some drawbacks which is why I am looking for alternatives.  
  
**General:**  
I love the automatic connection handling but I'd really like some callbacks when no connection can be established to be able to report this to a UI or Logs.   
  
**Specific:**  
I run into an issue where on Windows + Localhost with a mosuqitto broker in between the latency is `>100ms`  
  
Beneath you can find some shortened testing code, this is embeddedin a larger application but (hopefully) shows all the relevant calls.  
  
```c++  
  
  
std::shared_ptr<async_mqtt5::mqtt_client<boost::asio::ip::tcp::socket, std::monostate>> client;  
  
  
void  
requestMessage()  
{  
  auto cb = [](async_mqtt5::error_code ec,  
                                      std::string topic,  
                                      std::string payload,  
                                      async_mqtt5::publish_props props) {  
    // Recv handling here  
  
    requestMessage();  
  };  
  
  client->async_receive(cb);  
}  
  
int main() {  
  boost::asio::io_context ioc;  
  
  client = std::make_shared<async_mqtt5::mqtt_client<boost::asio::ip::tcp::socket>>(ioc);  
    
  std::string clientId = "...";  
  client->brokers("127.0.0.1")  
    .keep_alive(30)  
    .credentials(clientId)  
    .async_run(boost::asio::detached);  
    
  auto topics = getTopics();  
    
  client->async_subscribe(topics,  
                            async_mqtt5::subscribe_props{},  
                            make_safe_callback([this](async_mqtt5::error_code,  
                                                      std::vector<async_mqtt5::reason_code>,  
                                                      async_mqtt5::suback_props) {  
                              requestMessage();  
                            }));  
  
// Later  
client->async_publish<async_mqtt5::qos_e::exactly_once>(  
  topic,  
  payload,  
  async_mqtt5::retain_e::no,  
  async_mqtt5::publish_props{},  
  [](async_mqtt5::error_code ec, auto, auto) {  
    // Logs errors  
  });  
}  
```  
  
I use this across two processes with a mosquitto broker in the middle (`mosquitto version 2.0.15`).  
  
The two processes play ping pong with messages and I measure the time between after the `async_publish` call until the received handler is called.  
  
Example Payload:  
```json  
{  
  "discriminator": "SomePayload",  
  "payload": "This is a message"  
}  
  
```  
  
This comes out to be roughly: `120ms` which seems extremely long. Plugging in paho-mqtt-cpp, instead I get around `3ms`.  
On Docker(WSL) I get around `50ms` with the mosquitto broker and `15ms` with EMQX, this is where paho-mqtt-cpp has issues and reports `100ms`. (Same code on both machines, using Clang/MSVC respectively)  
  
Any ideas where this long time could be coming from?

---

## Comment 1

> Username: Faaux  
> Created at: 2024-10-18 12:22:58 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/17#issuecomment-2422352288  

Please ignore, error on my side :(
