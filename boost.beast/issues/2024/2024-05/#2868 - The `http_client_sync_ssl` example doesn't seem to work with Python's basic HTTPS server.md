# #2868 - The `http_client_sync_ssl` example doesn't seem to work with Python's basic HTTPS server [Closed]

> Username: EelisVP  
> Created at: 2024-05-15 10:12:38 UTC  
> Updated at: 2024-05-28 20:09:50 UTC  
> Closed at: 2024-05-28 20:09:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2868  

Beast version: 353  
Compiler:  GCC 11.4  
  
Steps to reproduce:  
  
* First generate key/cert files:  
  
      openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365  
  
* Then make `server.py`:  
  
      from http.server import HTTPServer, BaseHTTPRequestHandler  
      import ssl  
  
      class SimpleHTTPRequestHandler(BaseHTTPRequestHandler):  
          def do_GET(self):  
              self.send_response(200)  
              self.end_headers()  
              self.wfile.write(b'Hello')  
  
      httpd = HTTPServer(('0.0.0.0', 51733), SimpleHTTPRequestHandler)  
  
      httpd.socket = ssl.wrap_socket (httpd.socket,  
              keyfile="key.pem",  
              certfile='cert.pem', server_side=True)  
  
      httpd.serve_forever()  
  
* Run it with `python3 server.py`, and verify with a browser that opening `https://localhost:51733` works and shows `Hello`.  
  
* Now take Beast's `http_client_sync_ssl.cpp` example, change `ssl::verify_peer` to `ssl::verify_none`, compile it, and run:  
  
      ./a.out localhost 51733 /ding  
    
The result I get is:  
  
    Error: stream truncated [asio.ssl.stream:1]  
  
This error happens during the call to `http::read`. In the output of `server.py`, I do see that the request for `/ding` was received.  
  
I don't know whether the bug is in Python's `http.server` module or in Boost.Beast, but I figure the intent is for the two to work together, so might be worth investigating.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-05-16 06:02:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2114098425  

This is a known issue with some of the web servers in the wild; they don't perform a proper SSL shutdown procedure and close the TCP socket unexpectedly. For example, if you try connecting to google.com, you would get that error, but not when connecting to github.com.  
  
By the way, because this error happens exactly when you want to close the SSL stream, you can safely ignore this error (as all internet browsers do):  
  
```C++  
beast::error_code ec;  
stream.shutdown(ec);  
if (ec == net::error::eof || ec == net::ssl::error::stream_truncated)  
    ec = {};  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-05-23 13:51:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127174837  

@ashtum we should document this, where the Table of Contents entry for the paragraph says "stream_truncated error" so people can find it easily

---

## Comment 3

> Username: EelisVP  
> Created at: 2024-05-23 14:06:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127216631  

Thanks for the help!  
  
I'm still struggling to figure out how to modify the example code to get it to print the "Hello" response from the server:  
  
In the client I tried replacing:  
  
    http::read(stream, buffer, res);  
          
with:  
  
    boost::beast::error_code myec;  
    http::read(stream, buffer, res, myec);  
    if(myec && myec != ssl::error::stream_truncated)  
        throw beast::system_error{myec};  
  
but this doesn't seem to do the trick. I still only get to see the `HTTP/1.1 200 OK` part, not the `Hello`  
  
    ee@vp:~/sand/beasttest % g++ main.cpp -lssl -lcrypto && ./a.out localhost 51733 /                                                                  
    HTTP/1.1 200 OK  
  
    Error: stream truncated  
    ee@vp:~/sand/beasttest % wget --no-check-certificate -q -O - https://localhost:51733                                                               
    Hello

---

## Comment 4

> Username: ashtum  
> Created at: 2024-05-23 14:22:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127254531  

@EelisVP In client mode, `http::read(stream, buffer, res, myec)` should complete successfully. The `net::ssl::error::stream_truncated` error only occurs during the call to `stream.shutdown(ec)`.  
  
What type of response body are you using? Could you please provide a minimal reproducible example of your issue?  
I recommend first trying the `http_client_sync_ssl.cpp` example without any modifications to see what it prints.

---

## Comment 5

> Username: EelisVP  
> Created at: 2024-05-23 14:29:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127271725  

@ashtum As described in the opening post, the issue can be reproduced by taking `http_client_sync_ssl.cpp` and only changing `ssl::verify_peer` to `ssl::verify_none` (because the server's certificate was self-signed). With only this modification, I get this result:  
```  
ee@vp:~/sand/beasttest % g++ -I /home/eelis/apps/sources/boost_1_85_0 main.cpp -lssl -lcrypto && ./a.out localhost 51733 /   
Error: stream truncated [asio.ssl.stream:1]  
```

---

## Comment 6

> Username: ashtum  
> Created at: 2024-05-23 14:47:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127325594  

It seems you successfully get a response from the server, but it is just empty. Note that the target in the `wget --no-check-certificate -q -O - https://localhost:51733` command is empty, while in `./a.out localhost 51733 /`, the target is "/".  
What happens if you try `./a.out localhost 51733 ""`?

---

## Comment 7

> Username: EelisVP  
> Created at: 2024-05-23 14:51:08 UTC  
> Updated at: 2024-05-23 14:51:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127334845  

> Note that the target in the `wget --no-check-certificate -q -O - https://localhost:51733` command is empty, while in `./a.out localhost 51733 /`, the target is "/".  
  
Nah, `wget` asks for `/` automatically if you don't provide a target. So wgetting `https://localhost:51733` is the same as wgetting `https://localhost:51733/`.  
  
> What happens if you try `./a.out localhost 51733 ""`?  
  
Same result (because the server doesn't look at the target at all).

---

## Comment 8

> Username: ashtum  
> Created at: 2024-05-23 14:59:48 UTC  
> Updated at: 2024-05-23 15:00:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127359975  

Does `http::read(stream, buffer, res, myec)` complete without any errors, or are you getting `net::ssl::error::stream_truncated` on that line? Could you try setting `req.keep_alive(true);`?

---

## Comment 9

> Username: EelisVP  
> Created at: 2024-05-23 15:04:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127373284  

> Does `http::read(stream, buffer, res, myec)` complete without any errors, or are you getting `net::ssl::error::stream_truncated` on that line?  
  
If I use:  
  
    boost::beast::error_code myec;  
    http::read(stream, buffer, res, myec);  
    if(myec) std::cout << "oh there was an error: " << myec << std::endl;  
  
then I do see `oh there was an error: asio.ssl.stream:1` at runtime, so it's really the `read` that's reporting the error.  
  
> Could you try setting `req.keep_alive(true);`?  
  
Sure. No effect.

---

## Comment 10

> Username: ashtum  
> Created at: 2024-05-23 15:41:08 UTC  
> Updated at: 2024-05-23 17:52:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127452115  

It seems we need to manually set the end of stream when parser completes with `ssl::error::stream_truncated` error, this should work:  
  
```cpp  
http::response_parser<http::dynamic_body> p;  
beast::error_code ec;  
http::read(stream, buffer, p, ec);  
  
if(ec == net::ssl::error::stream_truncated && p.need_eof())  
    p.put_eof(ec);  
  
std::cout << p.get() << std::endl;  
```

---

## Comment 11

> Username: ashtum  
> Created at: 2024-05-23 17:47:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2127729274  

OK, the reason the Beast parser needs a manual EOF is that your Python server doesn't set the `Content-Length` header nor does it perform a proper SSL shutdown procedure to indicate the EOF.  
  
If you change the Python code for the server to the following, it should work fine:  
  
```python  
class SimpleHTTPRequestHandler(BaseHTTPRequestHandler):  
    def do_GET(self):  
        message = b'hello'  
        self.send_response(200)  
        self.send_header("Content-type", "text/plain")  
        self.send_header('Content-Length', str(len(message)))  
        self.end_headers()  
        self.wfile.write(message)  
```

---

## Comment 12

> Username: EelisVP  
> Created at: 2024-05-24 06:59:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2128718726  

@ashtum Ah fantastic, with that change in the client, I do indeed get the `hello`! Thanks!

---

## Comment 13

> Username: ashtum  
> Created at: 2024-05-24 07:36:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2868#issuecomment-2128807126  

Yes, the only way to get it to work in Beast is to manually set the EOF on the parser.  
  
The reason this is not the default behavior is that it poses a security risk. Without a `Content-Length` header, the body length is determined by EOF, which allows an attacker to close the connection and truncate the body contents. Therefore, an HTTPS server should either perform a proper TLS shutdown or avoid relying on EOF by setting the `Content-Length` header.  
  
If you try using curl on your server, you will see it also completes with an error.
