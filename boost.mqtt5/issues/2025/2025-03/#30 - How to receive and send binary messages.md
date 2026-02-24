# #30 - How to receive and send binary messages [Closed]

> Username: vhe1  
> Created at: 2025-03-24 15:33:49 UTC  
> Updated at: 2025-03-25 12:24:59 UTC  
> Closed at: 2025-03-25 12:24:58 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/30  

Hi!  
[async_receive](https://www.boost.org/doc/libs/master/libs/mqtt5/doc/html/mqtt5/ref/boost__mqtt5__mqtt_client/async_receive.html) has a string payload.  
  
Is there any way to  get at the byte payload, in case the message is binary?  
Same for async_publish, of course.

---

## Comment 1

> Username: ksimicevic  
> Created at: 2025-03-25 09:01:02 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/30#issuecomment-2750556289  

Hi!  
  
You can freely send binary data using our existing interface. You can convert any binary data into `std::string` and it will publish/receive as expected.  
  
Take a look at the following example:  
```  
// Assume we subscribed to the topic "test" earlier...  
  
constexpr auto use_nothrow_awaitable = boost::asio::as_tuple(boost::asio::deferred);  
  
std::string msg = { 'h', 'i', 0, '!', 2, 3, 0 };  
auto [ec, rc, props] = co_await client.async_publish<boost::mqtt5::qos_e::at_least_once>(  
    "test", msg, boost::mqtt5::retain_e::no, {}, use_nothrow_awaitable  
);  
  
auto [pec, topic, payload, publish_props] = co_await client.async_receive(use_nothrow_awaitable);  
std::cout << payload.size() << std::endl; // Will print 7  
          
for (auto c : payload)  
    std::cout << int(c) << std::endl; // Will print numeric representation of each element in msg as expected  
```  
  
If you have your binary data stored in, let's say, `std::vector<uint8_t>`, then you will have to copy your data while converting it into `std::string` with:  
```  
std::vector<uint8_t> data = ....;  
std::string s { data.cbeing(), data.cend() };  
```  
  
Hope this helps!

---

## Comment 2

> Username: vhe1  
> Created at: 2025-03-25 12:24:58 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/30#issuecomment-2751087916  

It definitely does. Thank you!
