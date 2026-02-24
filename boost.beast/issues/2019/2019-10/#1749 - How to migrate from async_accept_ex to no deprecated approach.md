# #1749 - How to migrate from async_accept_ex to no deprecated approach? [Closed]

> Username: redboltz  
> Created at: 2019-10-30 07:21:12 UTC  
> Updated at: 2019-10-31 08:03:07 UTC  
> Closed at: 2019-10-31 04:26:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1749  

### Version of Beast  
Beast version: 266  
Boost version: 1.71.0  
  
### Overview  
  
According to [CHANGELOG.md](https://github.com/boostorg/beast/blob/develop/CHANGELOG.md), since the version 216, async_accept_ex has been deprecated.   
  
> * Overloads of the following functions which accept a Decorator are deprecated:  
>    * accept, accept_ex  
>    * handshake, handshake_ex  
>    * async_accept, async_accept_ex  
>    * async_handshake, async_handshake_ex  
>  
> Actions Required:  
> * Code which passes decorator to any `websocket::stream` member function should call `stream::set_option` instead with a newly constructed stream_base::decorator object containing the decorator. Alternatively, the macro `BOOST_BEAST_ALLOW_DEPRECATED` may be defined to 1.  
  
I'd like to replace no deprecated approach my existing code but I'm not sure how to do that.  
  
### Steps necessary to reproduce the problem  
  
Here is the essence of the code that I want to replace. It is the server side code.  
  
```cpp  
// [begin] I want to replace no deprecated approach   
ws.async_accept_ex(  
    *request,  
    [request]  
    (boost::beast::websocket::response_type& m) {  
        auto it = request->find("Sec-WebSocket-Protocol");  
        if (it != request->end()) {  
            std::cout << "name :" << it->name() << std::endl;  
            std::cout << "value:" << it->value() << std::endl;  
            m.insert(it->name(), it->value());  
        }  
    },  
    [&]  
    (boost::system::error_code const& e) {  
        std::cout << "ws accept error: ec = " << e.message() << std::endl;  
    }  
);  
// [end] I want to replace no deprecated approach   
```  
  
The client is [MQTT.js](https://github.com/mqttjs/MQTT.js).  
  
```js  
var mqtt = require('mqtt')  
var client  = mqtt.connect('ws://127.0.0.1:12345') // port should be the same as server  
```  
  
I guess that I should use `stream::set_option` instead of `m.insert(it->name(), it->value())`. I'd like to know how to do that.  
  
Here is a minimal complete code that describes the issue:  
  
```cpp  
#include <iostream>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio.hpp>  
  
namespace as = boost::asio;  
namespace bs = boost::beast;  
  
int main(int argc, char* argv[]) {  
    if (argc != 2) {  
        std::cout << "Usage: " << argv[0] << " port" << std::endl;  
        return -1;  
    }  
  
    as::io_context ioc;  
    as::ip::tcp::acceptor acceptor(ioc, as::ip::tcp::endpoint(as::ip::tcp::v4(), std::stoul(argv[1])));  
    bs::websocket::stream<as::ip::tcp::socket> ws(ioc);  
  
    acceptor.async_accept(  
        ws.next_layer(),  
        [&]  
        (boost::system::error_code e) {  
            std::cout << "accept handler called" << std::endl;  
            std::cout << e.message() << std::endl;  
            auto sb = std::make_shared<boost::asio::streambuf>();  
            auto request = std::make_shared<bs::http::request<bs::http::string_body>>();  
            bs::http::async_read(  
                ws.next_layer(),  
                *sb,  
                *request,  
                [&, sb, request]  
                (boost::system::error_code const& e, std::size_t) {  
                    std::cout << "read error: ec = " << e.message() << std::endl;  
                    if (bs::websocket::is_upgrade(*request)) {  
                        std::cout << "upgrade request" << std::endl;  
  
                        // [begin] I want to replace no deprecated approach   
                        ws.async_accept_ex(  
                            *request,  
                            [request]  
                            (boost::beast::websocket::response_type& m) {  
                                auto it = request->find("Sec-WebSocket-Protocol");  
                                if (it != request->end()) {  
                                    std::cout << "name :" << it->name() << std::endl;  
                                    std::cout << "value:" << it->value() << std::endl;  
                                    m.insert(it->name(), it->value());  
                                }  
                            },  
                            [&]  
                            (boost::system::error_code const& e) {  
                                std::cout << "ws accept error: ec = " << e.message() << std::endl;  
                            }  
                        );  
                        // [end] I want to replace no deprecated approach   
  
                    }  
                }  
            );  
        }  
    );  
    ioc.run();  
}  
```  
  
Output:  
  
```  
accept handler called  
Success  
read error: ec = Success  
upgrade request  
name :Sec-WebSocket-Protocol  
value:mqtt  
ws accept error: ec = Success  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-30 12:36:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1749#issuecomment-547880605  

https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_websocket/decorator.html

---

## Comment 2

> Username: redboltz  
> Created at: 2019-10-31 04:26:00 UTC  
> Updated at: 2019-10-31 08:03:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1749#issuecomment-548211812  

@vinniefalco , thank you!  
  
I updated my code as follows and it works as I expected.  
  
```cpp  
// [begin] I want to replace no deprecated approach  
auto it = request->find("Sec-WebSocket-Protocol");  
if (it != request->end()) {  
    std::cout << "name :" << it->name() << std::endl;  
    std::cout << "value:" << it->value() << std::endl;  
    ws.set_option(  
        bs::websocket::stream_base::decorator(  
            [name = it->name(), value = it->value()]  
             (bs::websocket::response_type& res) {  
                res.set(name, value);  
            }  
        )  
    );  
}  
ws.async_accept(  
    *request,  
    [&]  
    (boost::system::error_code const& e) {  
        std::cout << "ws accept error: ec = " << e.message() << std::endl;  
    }  
);  
// [end] I want to replace no deprecated approach  
```
