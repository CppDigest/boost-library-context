# #2999 - async_read occasionally doesn't abort when data is sent after close in WebSocket stream [Closed]

> Username: Nerixyz  
> Created at: 2025-04-12 08:52:51 UTC  
> Updated at: 2025-04-26 17:47:01 UTC  
> Closed at: 2025-04-26 17:47:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2999  

A full reproduction can be found on https://github.com/Nerixyz/test-mini-cpp-project/tree/chore/test-asio-ci. I was only able to reproduce this on macOS (in GitHub Actions).  
  
The Go library https://github.com/coder/websocket had a bug where it would send data after a close. This has been fixed in version v1.8.13 with https://redirect.github.com/coder/websocket/pull/476, but is still present in older versions. In the reproduction, I'm using v1.8.12.  
Even though this is a bug in the Go library, it revealed that Beast's WebSockets can get stuck in `async_read` even though they received a close frame (maybe I missed something, but even using cancellation signals, the operation can't be stopped).  
  
The client code for Beast is based on https://github.com/boostorg/beast/blob/1d8dfae490125bd63efedfa7a3ecf20e8e9b54a5/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp.   
  
<details><summary>Diff</summary>  
  
```diff  
diff --git a/base.cpp b/src/main.cpp  
index 22525c2..4cecbd9 100644  
--- a/base.cpp  
+++ b/src/main.cpp  
@@ -13,17 +13,17 @@  
 //  
 //------------------------------------------------------------------------------  
   
-#include "example/common/root_certificates.hpp"  
-  
 #include <boost/beast/core.hpp>  
 #include <boost/beast/websocket.hpp>  
 #include <boost/beast/websocket/ssl.hpp>  
 #include <boost/asio/ssl.hpp>  
 #include <boost/asio/strand.hpp>  
+#include <boost/beast/websocket/stream.hpp>  
 #include <cstdlib>  
 #include <iostream>  
 #include <memory>  
 #include <string>  
+#include <deque>  
   
 namespace beast = boost::beast;         // from <boost/beast.hpp>  
 namespace http = beast::http;           // from <boost/beast/http.hpp>  
@@ -48,7 +48,7 @@ class session : public std::enable_shared_from_this<session>  
     websocket::stream<ssl::stream<beast::tcp_stream>> ws_;  
     beast::flat_buffer buffer_;  
     std::string host_;  
-    std::string text_;  
+    std::deque<std::string> messages_;  
   
 public:  
     // Resolver and socket require an io_context  
@@ -63,8 +63,7 @@ public:  
     void  
     run(  
         char const* host,  
-        char const* port,  
-        char const* text)  
+        char const* port)  
     {  
         // Set SNI Hostname (many hosts need this to handshake successfully)  
         if(! SSL_set_tlsext_host_name(ws_.next_layer().native_handle(), host))  
@@ -81,7 +80,18 @@ public:  
   
         // Save these for later  
         host_ = host;  
-        text_ = text;  
+        messages_ = {  
+            std::string(1 << 15, 'A'),  
+            std::string(1 << 15, 'A'),  
+            std::string(1 << 15, 'A'),  
+            std::string(1 << 15, 'A'),  
+            "foo",  
+            "foo",  
+            "foo",  
+            "foo",  
+            "foo",  
+            "/CLOSE",  
+        };  
   
         // Look up the domain name  
         resolver_.async_resolve(  
@@ -158,7 +168,7 @@ public:  
             }));  
   
         // Perform the websocket handshake  
-        ws_.async_handshake(host_, "/",  
+        ws_.async_handshake(host_, "/echo",  
             beast::bind_front_handler(  
                 &session::on_handshake,  
                 shared_from_this()));  
@@ -170,9 +180,20 @@ public:  
         if(ec)  
             return fail(ec, "handshake");  
   
+        ws_.control_callback([](auto ty, auto) {  
+            if (ty == websocket::frame_type::close) {  
+                std::cout << "received close frame" << std::endl;  
+            }  
+        });  
+      
+        ws_.async_read(  
+            buffer_,  
+            beast::bind_front_handler(  
+                &session::on_read,  
+                shared_from_this()));  
         // Send the message  
         ws_.async_write(  
-            net::buffer(text_),  
+            net::buffer(messages_.front()),  
             beast::bind_front_handler(  
                 &session::on_write,  
                 shared_from_this()));  
@@ -184,15 +205,18 @@ public:  
         std::size_t bytes_transferred)  
     {  
         boost::ignore_unused(bytes_transferred);  
+        if (messages_.empty()) {  
+            return;  
+        }  
+        messages_.pop_front();  
   
         if(ec)  
             return fail(ec, "write");  
-  
-        // Read a message into our buffer  
-        ws_.async_read(  
-            buffer_,  
+          
+        ws_.async_write(  
+            net::buffer(messages_.front()),  
             beast::bind_front_handler(  
-                &session::on_read,  
+                &session::on_write,  
                 shared_from_this()));  
     }  
   
@@ -206,24 +230,15 @@ public:  
         if(ec)  
             return fail(ec, "read");  
   
+        std::cout << "read " << bytes_transferred << std::endl;  
+        buffer_.consume(bytes_transferred);  
         // Close the WebSocket connection  
-        ws_.async_close(websocket::close_code::normal,  
+        ws_.async_read(  
+            buffer_,  
             beast::bind_front_handler(  
-                &session::on_close,  
+                &session::on_read,  
                 shared_from_this()));  
     }  
-  
-    void  
-    on_close(beast::error_code ec)  
-    {  
-        if(ec)  
-            return fail(ec, "close");  
-  
-        // If we get here then the connection is closed gracefully  
-  
-        // The make_printable() function helps print a ConstBufferSequence  
-        std::cout << beast::make_printable(buffer_.data()) << std::endl;  
-    }  
 };  
   
 //------------------------------------------------------------------------------  
@@ -231,7 +246,7 @@ public:  
 int main(int argc, char** argv)  
 {  
     // Check command line arguments.  
-    if(argc != 4)  
+    if(argc != 3)  
     {  
         std::cerr <<  
             "Usage: websocket-client-async-ssl <host> <port> <text>\n" <<  
@@ -241,22 +256,15 @@ int main(int argc, char** argv)  
     }  
     auto const host = argv[1];  
     auto const port = argv[2];  
-    auto const text = argv[3];  
   
     // The io_context is required for all I/O  
     net::io_context ioc;  
   
     // The SSL context is required, and holds certificates  
-    ssl::context ctx{ssl::context::tlsv12_client};  
-  
-    // Verify the remote server's certificate  
-    ctx.set_verify_mode(ssl::verify_peer);  
-  
-    // This holds the root certificate used for verification  
-    load_root_certificates(ctx);  
+    ssl::context ctx{ssl::context::tls_client};  
   
     // Launch the asynchronous operation  
-    std::make_shared<session>(ioc, ctx)->run(host, port, text);  
+    std::make_shared<session>(ioc, ctx)->run(host, port);  
   
     // Run the I/O service. The call will return when  
     // the socket is closed.  
  
```  
</details>   
  
To reproduce:  
  
```shell  
cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo  
ninja -C build  
go get && go build  
./test-mini-cpp-project 127.0.0.1:9050 &  
# wait a bit for the server to start up (like 3s)  
cd build  
ctest -V --repeat until-fail:1000  
```  
  
That's what CI does as well. Here's example output: https://github.com/Nerixyz/test-mini-cpp-project/actions/runs/14417986336/job/40437004130 - as you can see, the close frame was received but `async_read` didn't abort, and the test timed out. You can also see that this isn't fully deterministic and takes some tries to reproduce.  
  
This looks a bit similar to https://github.com/boostorg/beast/issues/2989, but if I understand that issue correctly, then `async_read` doesn't _return_ - here it returns but the operation doesn't abort.

---

## Comment 1

> Username: sehe  
> Created at: 2025-04-12 14:19:21 UTC  
> Updated at: 2025-04-12 14:19:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2999#issuecomment-2798849469  

I cannot reproduce this assuming Boost 1.86. Using a Nix dev-shell (platform-agnostic reproducible) using [flake.nix](https://github.com/sehe/test-mini-cpp-project/blob/sehe/flake.nix), consistently succeeds all runs:  
  
![Image](https://github.com/user-attachments/assets/2674e539-d547-4e9e-9ea1-86ee9ca83ec8)  
  
Can you compare notes to zoom in on relevant factors to reproduce?

---

## Comment 2

> Username: Nerixyz  
> Created at: 2025-04-12 15:09:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2999#issuecomment-2798867771  

I don't know nix too well, but I managed to get it working on GitHub Actions on macOS: https://github.com/Nerixyz/test-mini-cpp-project/blob/ci/run-in-gh/.github/workflows/build.yml  
  
It fails there as well: https://github.com/Nerixyz/test-mini-cpp-project/actions/runs/14420723435/job/40442935602  
  
I'd assume this has something to do with macOS, since I can't reproduce it on Windows nor on Linux. It happens on both Intel and ARM (`macos-13` runners are Intel macs, [docs](https://docs.github.com/en/actions/using-github-hosted-runners/using-github-hosted-runners/about-github-hosted-runners#standard-github-hosted-runners-for-public-repositories)).

---

## Comment 3

> Username: sehe  
> Created at: 2025-04-12 15:41:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2999#issuecomment-2798879900  

> I don't know nix too well, but I managed to get it working on GitHub Actions on macOS: https://github.com/Nerixyz/test-mini-cpp-project/blob/ci/run-in-gh/.github/workflows/build.yml  
  
Nice work getting Nix up and running in the CI action so quickly  
  
> It fails there as well: https://github.com/Nerixyz/test-mini-cpp-project/actions/runs/14420723435/job/40442935602  
  
Ah. good. I somehow completely missed that macOS was mentioned as a factor. Excuse my confusion

---

## Comment 4

> Username: ashtum  
> Created at: 2025-04-12 18:09:23 UTC  
> Updated at: 2025-04-12 18:09:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2999#issuecomment-2798933125  

I couldn't reproduce it on a `linux/arm64 ubuntu:24.04`  image running in QEMU. (Boost 1.87.0)  
I'm afraid the issue might be the 5-second timeout in the CI. Could you increase the timeout and see what happens? I believe you can set it to a much larger value, like 20 minutes, since as long as it’s running, it indicates there has been no freezing. If it freezes, it should freeze indefinitely.

---

## Comment 5

> Username: ashtum  
> Created at: 2025-04-12 19:20:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2999#issuecomment-2798984096  

> Even though this is a bug in the Go library, it revealed that Beast's WebSockets can get stuck in async_read even though they received a close frame (maybe I missed something, but even using cancellation signals, the operation can't be stopped).  
  
Beast doesn't close the stream immediately after receiving a close; it sends a close frame in response (as required by the RFC). Also in this case because the next layer type is `ssl::stream`, it performs the SSL shutdown handshake as well. If the implementation on the server side delays any of these steps, it becomes noticeable on the client side as well.

---

## Comment 6

> Username: Nerixyz  
> Created at: 2025-04-12 21:08:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2999#issuecomment-2799039858  

> I believe you can set it to a much larger value, like 20 minutes, since as long as it’s running, it indicates there has been no freezing.  
  
I set a 20min (1200s) timeout and it failed: https://github.com/Nerixyz/test-mini-cpp-project/actions/runs/14423029505

---

## Comment 7

> Username: ashtum  
> Created at: 2025-04-13 07:16:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2999#issuecomment-2799836525  

It looks like we're relying on `idle_timeout` to timeout the close procedure during the `async_read` operation. Since your code uses `role_type::client`, `idle_timeout` is disabled by default. However, if we explicitly set it:  
https://github.com/ashtum/test-mini-cpp-project/blob/51fa43ea197b2b8703b159dca513cc5df34cccf6/src/main.cpp#L158  
The `async_read` operation completes with a timeout, and the tests pass:  
https://github.com/ashtum/test-mini-cpp-project/actions/runs/14427116730/job/40457447928#step:4:2739  
I believe the correct behavior would be to use `handshake_timeout`, as it is used in the `async_close` operation.
