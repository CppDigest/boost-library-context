# #2372 - Send image through websocket [Closed]

> Username: esuig  
> Created at: 2022-01-09 01:14:03 UTC  
> Updated at: 2022-06-14 17:39:03 UTC  
> Closed at: 2022-06-14 17:39:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2372  

Hello,  
  
I'm trying to approach the wonderful world of websockets using the bost.beast library. I would like to stream images from a C++ server to an html5 client. However I had no success until now. I adapted the code from here https://github.com/iboB/html5-gui-demo/tree/master/ws-simple as follows, just to start learning:  
  
```  
// HTML 5 GUI Demo  
// Copyright (c) 2019 Borislav Stanimirov  
//  
// Distributed under the MIT Software License  
// See accompanying file LICENSE.txt or copy at  
// https://opensource.org/licenses/MIT  
//  
#include <iostream>  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <string>  
#include <thread>  
#include <memory>  
#include <set>  
#include <deque>  
#include <chrono>  
#include <cassert>  
  
#include "opencv2/opencv.hpp"  
#include "opencv2/imgproc/imgproc.hpp"  
#include "opencv2/photo.hpp"  
#include "opencv2/highgui.hpp"  
#include "opencv2/imgcodecs.hpp"  
#include "opencv2/highgui/highgui.hpp"  
  
#include "base64.h"  
  
using namespace std;  
using namespace std::chrono_literals;  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
atomic_uint32_t sid = 0;  
  
struct Packet  
{  
    string textBuffer;  
    vector<uint8_t> binaryBuffer;  
    bool text = false;  
};  
  
using PacketPtr = shared_ptr<Packet>;  
  
class Session : public enable_shared_from_this<Session>  
{  
public:  
    Session(tcp::socket&& socket)  
        : m_id(++sid)  
        , m_ws(move(socket))  
    {  
        // set suggested timeout settings for the websocket  
        m_ws.set_option(websocket::stream_base::timeout::suggested(beast::role_type::server));  
  
        // set a decorator to change the Server of the handshake  
        m_ws.set_option(websocket::stream_base::decorator(  
            [](websocket::response_type& res)  
            {  
                res.set(http::field::server, string(BOOST_BEAST_VERSION_STRING) + "ws-simple-server");  
            }));  
  
        cout << "Created session " << m_id << '\n';  
    }  
  
    void run()  
    {  
        m_ws.async_accept(beast::bind_front_handler(&Session::onAccept, shared_from_this()));  
    }  
  
    void handler(const boost::system::error_code& error, // Result of operation.  
                 std::size_t bytes_transferred)// Number of bytes written from the  
                                          // buffers. If an error occurred,  
                                          // this will be less than the sum  
                                          // of the buffer sizes.                                        
    {  
        std::cout<<std::to_string(bytes_transferred)<<std::endl;  
        std::cout<<error<<std::endl;  
        std::cout<<error.message()<<std::endl;  
    }  
  
    void onAccept(beast::error_code e)  
    {  
        if (e)   
        {  
            return fail(e, "accept");  
        }  
  
        cv::Mat imgin;  
        imgin = cv::imread("pnggrad16rgb.png", cv::IMREAD_UNCHANGED);  
        int rows = imgin.rows;  
        std::vector<uchar> buffer;  
        cv::imencode(".png",imgin,buffer);  
        std::string s = base64_encode(buffer.data(), buffer.size());  
          
  
        m_ws.text(true);  
        auto handler = beast::bind_front_handler(&Session::handler, shared_from_this());  
        m_ws.async_write(net::buffer(s), std::move(handler));  
  
    }  
  
    void fail(beast::error_code e, const char* source)  
    {  
        cerr << "Session " << m_id << " error: " << e <<  " in " << source << '\n';  
    }  
  
    void close()  
    {  
        cout << "Session " << m_id << " closed \n";  
    }  
  
private:  
    const uint32_t m_id;  
    websocket::stream<tcp::socket> m_ws;  
  
    // io  
    beast::flat_buffer m_readBuf, m_writeBuf;  
  
    deque<PacketPtr> m_writeQueue;  
}; // end class Session  
  
class Server  
{  
public:  
    Server(tcp::endpoint endpoint)  
        : m_context(1)   
        , m_acceptor(m_context, endpoint)  
    {}  
  
    int run()  
    {  
        doAccept();  
        m_context.run();  
        return 0;  
    }  
  
    void doAccept()  
    {  
        m_acceptor.async_accept(beast::bind_front_handler(&Server::onAccept, this));  
    }  
  
    void onAccept(beast::error_code error, tcp::socket socket)  
    {  
        if (error)  
        {  
            cerr << "Server::onAccept error: " << error << '\n';  
            return;  
        }  
  
        auto session = make_shared<Session>(move(socket));  
        session->run();  
  
        doAccept();  
    }  
  
private:  
    net::io_context m_context;  
    tcp::acceptor m_acceptor;  
}; // end class Server  
  
int main()  
{  
    ///////////////////////////////////////////////////////////////////////////  
    // args  
    const auto address = boost::asio::ip::tcp::v4(); // net::ip::make_address(argAddr);  
    const uint16_t port = 7654;  
  
    ///////////////////////////////////////////////////////////////////////////  
    // run  
    Server server(tcp::endpoint(address, port));  
    return server.run();  
}  
  
```  
In the previous code the base64_encode function is taken from here: https://renenyffenegger.ch/notes/development/Base64/Encoding-and-decoding-base-64-with-cpp/  
  
While the client is like this (this is adapted from the repository at this link https://github.com/iboB/html5-gui-demo too):  
  
```  
<!DOCTYPE html>  
<html>  
	<head>  
		<title>  
			Image test  
		</title>  
        <script language="javascript" type="text/javascript">  
            "use strict";  
            var output;  
            var ws;  
      
  
            function init() {  
                output = document.getElementById("output");  
              
                let wsUri = 'ws://' + window.location.hostname+ ':7654';  
              
                ws = new WebSocket(wsUri);  
                ws.onopen = () => {  
                    writeToScreen('CONNECTED', output);  
                    doSend('WebSocket rocks!');  
                };  
                ws.onclose = () => writeToScreen('<p style="color: red;">DISCONNECTED</p>');  
                  
                ws.onmessage = function (evt) {  
                    console.log( "Message received :", evt.data );  
                    document.getElementById("picturetest").src = "data:image/png;base64," + evt.data;  
                    };  
                  
                ws.onerror = msg => writeToScreen('<p style="color: red;">ERROR: ' + msg.data + '</p>');  
            }  
  
            function writeToScreen(message) {  
                let p = document.createElement('p');  
                p.style.wordWrap = 'break-word';  
                p.innerHTML = message;  
  
                if (output.childNodes.length > 5) {  
                output.removeChild(output.childNodes[0]);  
                }  
                output.appendChild(p);  
            }  
  
            function doSend(message) {  
                writeToScreen("SENT: " + message);  
                ws.send(message);  
            }  
  
            window.addEventListener('load', init, false);  
  
            function wsSend() {  
                doSend(document.getElementById('data_to_send').value);  
            }  
  
        </script>  
	</head>  
	<body style="font-family: sans-serif; font-size: 1.5em">  
        <h1>TESTIMAGE</h1>  
        <img id="picturetest" src="" style="border:3px solid black" />  
        <br>  
      
        <div id="output"></div>  
	</body>  
</html>  
```  
  
However the image is not displayed and it seems I get an error system:14, Bad Address. Could you please help me in understanding what's I'm doing wrong? My next step is to perform a video stream, but first I would like to be able to stream a single image through websocket...

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-01-14 16:36:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2372#issuecomment-1013279075  

Sure, I'll replicate a test here.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-14 17:39:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2372#issuecomment-1155502864  

Is this resolved?
