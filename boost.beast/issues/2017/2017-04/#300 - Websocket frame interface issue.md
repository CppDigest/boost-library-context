# #300 - Websocket frame interface issue [Closed]

> Username: matinko  
> Created at: 2017-04-11 08:04:31 UTC  
> Updated at: 2017-04-19 15:43:43 UTC  
> Closed at: 2017-04-19 15:43:43 UTC  
> Url: https://github.com/boostorg/beast/issues/300  

Hi,  
first of all, thank you very much for great library. I really like that it is based on Boost.Asio, which makes it easy to understand and work with.  
  
However I am having troubles with using websocket frame interface. Following is the minimal working example which demonstrates my problem.  
  
```c++  
#include <beast/websocket.hpp>  
#include <boost/asio.hpp>  
#include <iostream>  
#include <string>  
#include <array>  
  
int main()  
{  
    // Normal boost::asio setup  
    std::string const host = "127.0.0.1";  
    boost::asio::io_service ios;  
    boost::asio::ip::tcp::resolver r{ios};  
    boost::asio::ip::tcp::socket sock{ios};  
    boost::asio::connect(sock,  
        r.resolve(boost::asio::ip::tcp::resolver::query{host, "42000"}));  
  
    // WebSocket connect and send message using beast  
    beast::websocket::stream<boost::asio::ip::tcp::socket&> ws{sock};  
    ws.handshake(host, "/");  
    ws.set_option(beast::websocket::message_type{beast::websocket::opcode::binary});  
  
    // Send some random binary message  
    std::array<char, 512> binary_message;  
    ws.write_frame(false, boost::asio::buffer(binary_message));  
  
    // THIS IS HACK, (needed to make this work)  
    ws.set_option(beast::websocket::message_type{beast::websocket::opcode::cont});  
  
    ws.write_frame(true, boost::asio::buffer(binary_message));  
  
    // Receive WebSocket message, print and close using beast  
    beast::streambuf sb;  
    beast::websocket::opcode op;  
    beast::error_code ec;  
    ws.close(beast::websocket::close_code::normal);  
    ws.read(op, sb, ec);  
  
    if (ec != beast::websocket::error::closed)  
    {  
        std::cerr << "Communication terminated in unexpected way." << std::endl;  
        return 1;  
    }  
  
    std::cout << "Successfully terminated." << std::endl;  
    return 0;  
}  
```  
I was forced to modify constructor of the class ``` beast::websocket::message_type ``` so it accepts ``` beast::websocket::opcode::cont ``` as argument. Then, before sending the second frame I have to call ```ws.set_option(beast::websocket::message_type{beast::websocket::opcode::cont});``` on the websocket.  
  
The problem is that if I do not do that, the server will report an error: *Protocol violation*. I am using the **websocket_async_echo_server.hpp** from your examples with slight (ad-hoc) modification that it uses ``` async_read_frame() ``` and it is not sending a response.  
  
```c++  
        void operator()(error_code ec)  
        {  
            using boost::asio::buffer;  
            using boost::asio::buffer_copy;  
            auto& d = *d_;  
            switch(d.state)  
            {  
            // did accept  
            case 0:  
                if(ec)  
                    return fail("async_accept", ec);  
  
            // start  
            case 1:  
                if(ec)  
                    return fail("async_handshake", ec);  
                d.db.consume(d.db.size());  
                // read message  
                d.state = 2;  
                //d.ws.async_read(d.op, d.db, d.strand.wrap(std::move(*this))); // ORIGINAL  
                d.ws.async_read_frame(d.fi, d.fb, d.strand.wrap(std::move(*this)));  
                return;  
  
            // got message  
            case 2:  
                if(ec == beast::websocket::error::closed)  
                    return;  
                if(ec)  
                    return fail("async_read", ec);  
                // ORIGINAL  
                // write message  
                //d.state = 1;  
                //d.ws.set_option(beast::websocket::message_type(d.op));  
                //d.ws.async_write(d.db.data(), d.strand.wrap(std::move(*this)));  
  
                if (!d.fi.fin)  
                {  
                    d.ws.async_read_frame(d.fi, d.fb, d.strand.wrap(std::move(*this)));  
                }  
                else // entire frame received  
                {  
                    std::cout << "Entire frame received" << std::endl;  
                    d.ws.async_read(d.op, d.db, d.strand.wrap(std::move(*this)));  
                }  
                return;  
            }  
        }  
```  
I was tracking *Protocol violation* error on the server side and it seems to come from file: **stream_base.hpp**:  
```c++  
        if(rd_.cont)  
        {  
            // new data frame when continuation expected  
            return err(close_code::protocol_error);  
        }  
```  
Finally, I am using **websocket_echo.cpp** from your examples as a server, where I removed ``` websocket::sync_echo_server ``` part.  
  
Thus in summary, when I use the HACK ```ws.set_option(beast::websocket::message_type{beast::websocket::opcode::cont});``` everything works as expected (At least seems so). When I remove this line from client I get *Protocol violation* error on server side.  
  
Could you please give me a hint what am I doing wrong?  
  
Thank you.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-13 01:30:57 UTC  
> Url: https://github.com/boostorg/beast/issues/300#issuecomment-293752030  

I am reviewing and investigating your issue, thanks for being so thorough that will help a ton!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-04-14 19:33:48 UTC  
> Url: https://github.com/boostorg/beast/issues/300#issuecomment-294220754  

I believe this branch contains a commit that reproduces your issue:  
https://github.com/vinniefalco/Beast/commits/b33  
  
This test:  
https://github.com/vinniefalco/Beast/blob/b33/test/websocket/stream.cpp#L808

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-15 02:17:23 UTC  
> Url: https://github.com/boostorg/beast/issues/300#issuecomment-294266226  

@matinko Sorry for the delay, I don't have access to my desktop machine. I have located the defect and committed a fix, can you verify it works? Thanks!  
https://github.com/vinniefalco/Beast/commits/b33

---

## Comment 4

> Username: matinko  
> Created at: 2017-04-16 13:06:30 UTC  
> Url: https://github.com/boostorg/beast/issues/300#issuecomment-294350890  

@vinniefalco Hi, just tested the branch b33 and I can say it fixed my problem. Thank you very much!
