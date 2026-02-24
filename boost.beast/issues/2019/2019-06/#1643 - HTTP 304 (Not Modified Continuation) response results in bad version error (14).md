# #1643 - HTTP 304 (Not Modified Continuation) response results in bad version error (14) [Closed]

> Username: ruurdadema  
> Created at: 2019-06-25 18:21:45 UTC  
> Updated at: 2019-06-26 12:01:05 UTC  
> Closed at: 2019-06-26 12:01:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1643  

Version of Beast: 1.69.0  
  
When receiving a 304 (Not Modified Continuation) response I run into an issue where I get a "Bad version (14)" error. With this response result the buffer still has 2 bytes left and probably causes the parsing to go wrong next round.  
  
However, I'm not sure if this behaviour is to be expected or that maybe the response I receive is malformed.  
  
Attached is a complete working example. Note line 87 where there is a workaround to make the code work without reporting an error.  
  
```  
#include <boost/asio.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
  
#define LOG(FORMAT, ...) printf(FORMAT " (line:%d)\n", ##__VA_ARGS__, __LINE__)  
  
class TestClient  
{  
public:  
    TestClient(boost::asio::io_context& ioc, const std::string& target) : m_ioc(ioc)  
    {  
        m_request.method(boost::beast::http::verb::get);  
        m_request.target(target);  
    }  
  
    void connect(const std::string& host, const std::string& port)  
    {  
        boost::system::error_code ec;  
  
        boost::asio::ip::tcp::resolver resolver(m_ioc);  
        auto const results = resolver.resolve(host, port);  
  
        boost::asio::connect(m_socket, results.begin(), results.end(), ec);  
  
        if (ec)  
        {  
            LOG("ASIO Connect error: %d : %s", ec.value(), ec.message().c_str());  
            return;  
        }  
  
        boost::asio::post(m_ioc, [this](){ requestAndResponse(); });  
    }  
  
    void requestAndResponse()  
    {  
        boost::system::error_code ec;  
  
        m_request.set("If-None-Match", m_etag);  
  
        boost::beast::http::write(m_socket, m_request, ec);  
  
        if (ec)  
        {  
            if (ec == boost::asio::error::bad_descriptor)  
            {  
                LOG("Socket closed (probably due to application shutdown)");  
            }  
            else  
            {  
                LOG("Error: %d : %s", ec.value(), ec.message().c_str());  
            }  
  
            return;  
        }  
  
        if (m_buffer.size() != 0)  
        {  
            LOG("Buffer not zero! (%lu)", m_buffer.size());  
        }  
  
        m_response.body().clear();  
        boost::beast::http::read(m_socket, m_buffer, m_response, ec);  
  
        if (ec)  
        {  
            if (ec == boost::asio::error::bad_descriptor)  
            {  
                LOG("Socket closed (probably due to application shutdown)");  
            }  
            else  
            {  
                LOG("Error: %d : %s", ec.value(), ec.message().c_str());  
            }  
  
            return;  
        }  
  
        if (m_response.result_int() == 200)  
        {  
            std::string etag(m_response.at("ETag"));  
            m_etag = etag;  
            LOG("200 OK, ETag=%s", m_etag.c_str());  
        }  
        else if (m_response.result_int() == 304)  
        {  
            // Workaround to prevent "Bad version (14) error"  
            // m_buffer.consume(m_buffer.size());  
            LOG("304 Not Modified Continuation");  
        }  
        else  
        {  
            LOG("%d Unexpected HTTP status", m_response.result_int());  
        }  
  
        boost::asio::post(m_ioc, [this](){ requestAndResponse(); });  
    }  
private:  
    boost::asio::io_context& m_ioc;  
    boost::asio::ip::tcp::socket m_socket {m_ioc};  
    boost::beast::flat_buffer m_buffer;  
    boost::beast::http::request<boost::beast::http::string_body> m_request;  
    boost::beast::http::response<boost::beast::http::string_body> m_response;  
    std::string m_etag {"0"};  
};  
  
int main()  
{  
    boost::asio::io_context ioc;  
  
    TestClient client(ioc, "/fromhex");  
    boost::asio::post(ioc, [&]() { client.connect("localhost", "8000"); });  
  
    // TestClient client(ioc, "/generate");  
    // boost::asio::post(ioc, [&]() { client.connect("localhost", "8000"); });  
  
    ioc.run();  
  
    LOG("Bye!");  
  
    return 0;  
}  
```  
  
And the packet as captured by Wireshark:  
  
```  
0000   ac 87 a3 2f fd 8b 00 01 f2 00 24 c7 08 00 45 00  .../......$...E.  
0010   00 dc 70 fa 40 00 40 06 96 59 c0 a8 58 8d c0 a8  ..p.@.@..Y..X...  
0020   58 ea 00 50 d8 d9 4c 98 1e 03 69 99 2f 2c 80 18  X..P..L...i./,..  
0030   08 28 6d 6f 00 00 01 01 08 0a 00 7b b5 a8 27 07  .(mo.......{..'.  
0040   5b e9 48 54 54 50 2f 31 2e 31 20 33 30 34 20 4e  [.HTTP/1.1 304 N  
0050   6f 74 20 4d 6f 64 69 66 69 65 64 0d 0a 43 6f 6e  ot Modified..Con  
0060   6e 65 63 74 69 6f 6e 3a 20 4b 65 65 70 2d 41 6c  nection: Keep-Al  
0070   69 76 65 0d 0a 43 6f 6e 74 65 6e 74 2d 4c 65 6e  ive..Content-Len  
0080   67 74 68 3a 20 30 0d 0a 43 61 63 68 65 2d 43 6f  gth: 0..Cache-Co  
0090   6e 74 72 6f 6c 3a 20 6e 6f 2d 63 61 63 68 65 0d  ntrol: no-cache.  
00a0   0a 41 63 63 65 73 73 2d 43 6f 6e 74 72 6f 6c 2d  .Access-Control-  
00b0   41 6c 6c 6f 77 2d 4f 72 69 67 69 6e 3a 20 2a 0d  Allow-Origin: *.  
00c0   0a 44 61 74 65 3a 20 54 68 75 2c 20 30 31 20 4a  .Date: Thu, 01 J  
00d0   61 6e 20 31 39 37 30 20 30 39 3a 30 35 3a 32 39  an 1970 09:05:29  
00e0   20 47 4d 54 0d 0a 0d 0a 0d 0a                     GMT......  
```

---

## Comment 1

> Username: djarek  
> Created at: 2019-06-25 18:25:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1643#issuecomment-505564925  

Could you provide a self-contained example that reproduces the issue and doesn't connect to an external server (e.g. using `test::stream`)?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-06-25 19:48:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1643#issuecomment-505595864  

I don't see any additional bytes in your packet capture

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-06-25 22:26:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1643#issuecomment-505645871  

Ah yes I see the additional bytes now:  
> `00e0   20 47 4d 54 0d 0a 0d 0a 0d 0a`  
  
There is an extra CRLF. You can filter them out of the `flat_buffer`. If you insist on accepting invalid HTTP, you can remove them if (and only if) the previous message was the 304, and the buffer contains CRLF as the first two bytes.

---

## Comment 4

> Username: ruurdadema  
> Created at: 2019-06-26 07:48:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1643#issuecomment-505761656  

Here is a python3 test script which runs a HTTP server on localhost:8000. The HTTP server responds to two different paths, `/generate` and `/fromhex`. The first one generates a 304 response and the latter sends bytes captured by Wireshark. I updated the testapplication code in my first question.  
  
```  
#!/usr/bin/env python3  
  
from http.server import HTTPServer, BaseHTTPRequestHandler  
from io import BytesIO  
import time  
  
class SimpleHTTPRequestHandler(BaseHTTPRequestHandler):  
    protocol_version = 'HTTP/1.1'  
  
    def do_GET(self):  
        print(self.path)  
  
        if (self.path == '/generate'):  
            self.send_response(304)  
            self.send_header('Content-length', '0')  
            self.send_header('Connection', 'Keep-Alive')  
            self.send_header('Cache-Control', 'no-cache')  
            self.send_header('Access-Control-Allow-Origin', '*')  
            self.end_headers()  
        elif (self.path == '/fromhex'):  
            self.wfile.write(bytearray.fromhex('485454502f312e3120333034204e6f74204d6f6469666965640d0a436f6e6e656374696f6e3a204b6565702d416c6976650d0a436f6e74656e742d4c656e6774683a20300d0a43616368652d436f6e74726f6c3a206e6f2d63616368650d0a4163636573732d436f6e74726f6c2d416c6c6f772d4f726967696e3a202a0d0a446174653a205468752c203031204a616e20313937302032333a32333a343420474d540d0a0d0a0d0a'))  
        else:  
            self.send_response(400)  
            self.send_header('Content-length', '0')  
            self.end_headers()  
  
        time.sleep(1)  
  
if __name__ == "__main__":  
    httpd = HTTPServer(('localhost', 8000), SimpleHTTPRequestHandler)  
    httpd.serve_forever()  
```  
  
Short user guide:  
- Run the python script. This should start a HTTP server compatible with the testapplication in my first post.  
- Run the testapplication. It should report error: Bad version 14.  
- Uncomment line 87 (m_buffer.consume(m_buffer.size());). The testapplication shouldn't crash anymore.  
- For testing the generated 304 response from the python script change the path `/fromhex` to `/generate`  
  
Down the road I discovered some things:  
  
- When testing with the `/generate` path, all goes well.  
- When testing with the `/fromhex` path the problem arises.  
- With the problematic packet Wireshark adds an extra field at the end of the interpretation:  
```  
Hypertext Transfer Protocol  
    Data (168 bytes)  
        Data: 485454502f312e3120333034204e6f74204d6f6469666965...  
        [Length: 168]  
```   
- This makes me suspect that the device I'm connecting to is sending malformed packets, however, I haven't been able to find where the problem lies.

---

## Comment 5

> Username: ruurdadema  
> Created at: 2019-06-26 10:37:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1643#issuecomment-505820187  

> Ah yes I see the additional bytes now:  
>   
> > `00e0   20 47 4d 54 0d 0a 0d 0a 0d 0a`  
>   
> There is an extra CRLF. You can filter them out of the `flat_buffer`. If you insist on accepting invalid HTTP, you can remove them if (and only if) the previous message was the 304, and the buffer contains CRLF as the first two bytes.  
  
Didn't see your comment in time...  
  
I can confirm that this is indeed the cause for the problem.  
Many thanks for looking into this and thanks for writing this outstanding library!

---

## Comment 6

> Username: ruurdadema  
> Created at: 2019-06-26 11:02:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1643#issuecomment-505827766  

btw. This is how I implemented the workaround, is there a cleaner way of doing this?  
```  
if (getStatus() == 304)  
{  
    if (m_buffer.size() == 2)  
    {  
        // If the two packets are CRLF clear the buffer  
        if (((char*)m_buffer.data().data())[0] == 0x0D &&  
            ((char*)m_buffer.data().data())[1] == 0x0A)  
        {  
            m_buffer.consume(2);  
        }  
    }  
}  
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-06-26 11:16:12 UTC  
> Updated at: 2019-06-26 11:16:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1643#issuecomment-505831666  

I would use `if (m_buffer.size() >= 2)`. You could also use `buffer_copy` to put the first 2 bytes into a local array of `char[2]` and then do the comparisons without the casts and calls to `data()`. As an added benefit, it would then work regardless of the type of dynamic buffer.

---

## Comment 8

> Username: ruurdadema  
> Created at: 2019-06-26 12:01:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1643#issuecomment-505844517  

Awesome! Many thanks!
