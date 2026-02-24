# #29 - connect_op.hpp is missing the start_clean configuration feature. [Open]

> Username: marsareacn  
> Created at: 2025-03-19 01:50:02 UTC  
> Updated at: 2025-03-21 16:05:32 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/29  

void send_connect() {  
        auto packet = control_packet<allocator_type>::of(  
            no_pid, get_allocator(),  
            encoders::encode_connect,  
            _ctx.creds.client_id,  
            _ctx.creds.username, _ctx.creds.password,  
            _ctx.keep_alive, false, _ctx.co_props, _ctx.will_msg  
        );  
the "false" is const, should be modified:  
    void send_connect() {  
        auto packet = control_packet<allocator_type>::of(  
            no_pid, get_allocator(),  
            encoders::encode_connect,  
            _ctx.creds.client_id,  
            _ctx.creds.username, _ctx.creds.password,  
            _ctx.keep_alive, _ctx.start_clean, _ctx.co_props, _ctx.will_msg  
        );

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-03-19 11:16:27 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/29#issuecomment-2736261924  

Hi, thanks for bringing this up.  
  
Since our library fully manages reconnections automatically, allowing `clean_start = true` could lead to unintended session resets at arbitrary times. Setting it only on the first connection doesn’t align with our library’s design, as we treat the first connection the same as any later reconnection.  
  
Given this, we’d like to understand your use case. Could you please share more details on why you need control over the clean_start flag?

---

## Comment 2

> Username: marsareacn  
> Created at: 2025-03-19 11:50:32 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/29#issuecomment-2736352868  

MQTTv5 supports automatic assignment of client_id by the server, meaning the client can set client_id = "" or nullptr. I have tested this with libraries like mosquitto and async_mqtt, and both support this feature. Additionally, documentation examples mention that client_id is optional and can be assigned by the server. However, a critical prerequisite for server-assigned client_id is that the start_clean (Clean Start) flag must be set to true. If start_clean is false, the server will neither assign a client_id nor respond to the connection request. I have validated this behavior on servers like EMQX, where the connection only succeeds when start_clean is true. The rationale for server-assigned client_id is to address scenarios where embedded devices lack the capability to generate unique client IDs.  
  
（https://www.boost.org/doc/libs/1_88_0_beta1/libs/mqtt5/doc/html/mqtt5/getting_started.html#mqtt5.getting_started.configuration）  
sample1 : client.brokers(cfg.brokers, cfg.port) // Set the Broker to connect to.  
    .credentials(cfg.client_id) // Set the Client Identifier. (optional)  
    .async_run(boost::asio::detached); // Start the Client.  
  
if start_clean == false , follow code will fail  
sample2: client.brokers(cfg.brokers, cfg.port) // Set the Broker to connect to.  
    .credentials("") // Set the Client Identifier. (optional)  
    .async_run(boost::asio::detached); // Start the Client.  
  
if start_clean == true, follow code will succeed  
sample3: client.brokers(cfg.brokers, cfg.port) // Set the Broker to connect to.  
    .credentials("") // Set the Client Identifier. (optional)  
    .async_run(boost::asio::detached); // Start the Client.  
  
the follow is my real code section:  
  
auto client = make_unique<mqtt_client<tcp::socket>>(*ctx)  
client->brokers(mqtt_server, atoi(mqtt_port.c_str())).connect_property(boost::mqtt5::prop::maximum_packet_size, 16 * 1024 * 1024).async_run(detached);  
string client_id;  
  
while (starts_ == starts && isRun && !isOver && *mqtt_linked)  
{  
    auto &id = client->connack_property(boost::mqtt5::prop::assigned_client_identifier);  
  
    if (id)  
    {  
        client_id = id.value();  
        break;  
    }  
  
    co_await steady_timer(*ctx, 100ms).async_wait(use_awaitable);  
}

---

## Comment 3

> Username: biljazovic  
> Created at: 2025-03-19 13:57:18 UTC  
> Updated at: 2025-03-19 14:01:27 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/29#issuecomment-2736745644  

> However, a critical prerequisite for server-assigned client_id is that the start_clean (Clean Start) flag must be set to true.  
  
This is a requirement in MQTT v3 and Clean Session flag. But in MQTT v5 restriction was lifted, from https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html "Summary of new features in MQTT v5.0", at the bottom:  
  
> In cases where the ClientID is assigned by the Server, return the assigned ClientID. This also lifts the restriction that Server assigned ClientIDs can only be used with Clean Session=1 connections.  
  
Therefore, EMQX does not conform to the v5 specification here.  
  
Edit: We tested this behaviour with Mosquitto and HiveMQ brokers, and both allow server-assigned client IDs with `clean_start=false`

---

## Comment 4

> Username: marsareacn  
> Created at: 2025-03-19 17:08:29 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/29#issuecomment-2737430932  

Yes, I just tested Mosquitto as well. When clean_start=false, the broker can still correctly assign a client_id. However, the latest EMQX version v5.8.5 does not allocate client IDs in this scenario (https://www.emqx.com/en/downloads-and-install/broker?os=Ubuntu). To achieve better compatibility,  the library function will automatically use clean_start=true when client_id is not configured, or expose the clean_start configuration capability for special use cases.  I like plan2. Can you improve it?  
  
plan1:  
void send_connect() {  
    auto packet = control_packet<allocator_type>::of(  
        no_pid, get_allocator(),  
        encoders::encode_connect,  
        _ctx.creds.client_id,  
        _ctx.creds.username, _ctx.creds.password,  
        _ctx.keep_alive, _ctx.creds.client_id.empty(), _ctx.co_props, _ctx.will_msg  
    );  
  
plan2:  
    auto packet = control_packet<allocator_type>::of(  
        no_pid, get_allocator(),  
        encoders::encode_connect,  
        _ctx.creds.client_id,  
        _ctx.creds.username, _ctx.creds.password,  
        _ctx.keep_alive, _ctx.start_clean, _ctx.co_props, _ctx.will_msg  
    );

---

## Comment 5

> Username: biljazovic  
> Created at: 2025-03-20 15:58:28 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/29#issuecomment-2740950152  

Thanks for the feedback and for opening the issue with EMQX.  
  
While EMQX enforces the `clean_start=true` requirement when no `client_id` is provided, this doesn't prevent orphaned sessions. A client can still set `session_expiry_interval` to a large value.  
  
Our current implementation adheres to the MQTT v5 specification and we are hesitant to change it. We'd also like to understand the specific use case where a client is unable to produce a unique identifier, as we haven't encountered a scenario where this is necessary.

---

## Comment 6

> Username: marsareacn  
> Created at: 2025-03-21 16:05:31 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/29#issuecomment-2743816607  

Thank you for your continued attention to the issues I've encountered.  
start_clean is a configurable item specified in the MQTTv5 standard. The current library hardcodes this variable as a constant "false", thereby disabling MQTTv5 functionality.  
  
Our current system collects sensor data where each sensor only uploads information using identical code. Fixing client_id in the code would result in duplicate client_ids when multiple sensors connect to the same broker. The sensors themselves don't require specific client_id values - broker-assigned random client_ids would suffice. This approach is similar to the implementation in:  
https://github.com/boostorg/mqtt5/blob/develop/example/publisher.cpp  
  
I've also communicated with EMQX regarding this matter. EMQX has indicated they won't support connection requests with both client_id="" and start_clean=false configurations.
