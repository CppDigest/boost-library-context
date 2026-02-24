# #2364 - Response not correctly set on handshake failure? [Closed]

> Username: matthijs  
> Created at: 2021-12-18 15:04:01 UTC  
> Updated at: 2021-12-22 11:04:44 UTC  
> Closed at: 2021-12-22 11:04:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2364  

As already said in thet title, response doesn't seem to be correctly set when an handshake failure occurs.  
  
### Version of Beast  
```c++  
#define BOOST_BEAST_VERSION 318  
```  
  
### Steps necessary to reproduce the problem  
https://github.com/matthijs/websocket-handshake  
  
I've made small change to the websocket_client_sync_ssl.cpp from the examples. The diff:  
```diff  
--- websocket_client_sync_ssl.cpp       2021-12-18 15:43:59.986833842 +0100  
+++ ../websocket-handshake/websocket_client_sync_ssl.cpp        2021-12-18 15:48:46.847431047 +0100  
@@ -13,7 +13,7 @@  
 //  
 //------------------------------------------------------------------------------  
  
-#include "example/common/root_certificates.hpp"  
+#include "root_certificates.hpp"  
  
 #include <boost/beast/core.hpp>  
 #include <boost/beast/ssl.hpp>  
@@ -96,7 +96,14 @@  
             }));  
  
         // Perform the websocket handshake  
-        ws.handshake(host, "/");  
+        boost::beast::websocket::response_type response;  
+        boost::system::error_code ec;  
+        ws.handshake(response, host, "/401", ec);  
+        std::cout << ec.message() << std::endl;  
+        std::cout << response << std::endl;  
+  
+        if (ec)  
+            return -1;  
  
         // Send the message  
         ws.write(net::buffer(std::string(text)));  
```  
  
When compiled call it with the following arguments:  
```bash  
% ./main httpstat.us 443 blabla  
The WebSocket handshake was declined by the remote peer  
HTTP/1.1 200 OK  
  
  
```  
I expect the above to return the 401 response from httpstat.us but it still shows the 200 response which I believe is incorrect. I am not able to inspect the response at this point.  
  
### All relevant compiler information  
Compiler: clang++-12  
Boost: 1.77.0  
OpenSSL: 1.1.1d

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-18 15:06:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-997215157  

Looks like `response` is default constructed and then not set.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-12-20 08:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-997710884  

I can't get your repo to build at the moment. Any special command line parameters I should set for cmake?

---

## Comment 3

> Username: matthijs  
> Created at: 2021-12-20 09:49:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-997764488  

I use the clang-12 compiler.  
```bash  
export CC=clang-12  
export CXX=clang++-12  
  
git clone https://github.com/matthijs/websocket-handshake.git  
mkdir websocket-handshake/build  
cd websocket-handshake/build  
cmake -DCMAKE_BUILD_TYPE=Debug ..  
make -j$(nproc)  
```  
Thats all I did (oh and I use the libc++ library instead of libstdc++).  
  
What kind of warning / error do you get?

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-12-20 09:59:15 UTC  
> Updated at: 2021-12-20 10:13:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-997771592  

```  
$ clang++ --version  
clang version 13.0.0 (Fedora 13.0.0-3.fc35)  
Target: x86_64-redhat-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
  
$ /usr/bin/cmake -DCMAKE_CXX_FLAGS="-stdlib=libc++" -DCMAKE_BUILD_TYPE=Debug -DCMAKE_MAKE_PROGRAM=/home/rhodges/.local/share/JetBrains/Toolbox/apps/CLion/ch-0/213.5744.254/bin/ninja/linux/ninja -DCMAKE_C_COMPILER=/usr/bin/clang -DCMAKE_CXX_COMPILER=/usr/bin/clang++ -DBoost_ROOT=/home/rhodges/work/libc++ -G Ninja /home/rhodges/github/matthijs/websocket-test  
...  
CMake Warning (dev) at cmake/compiler-options.cmake:16 (option):  
  Policy CMP0077 is not set: option() honors normal variables.  Run "cmake  
  --help-policy CMP0077" for policy details.  Use the cmake_policy command to  
  set the policy and suppress this warning.  
  
  For compatibility with older versions of CMake, option is clearing the  
  normal variable 'ENABLE_ALL_WARNINGS'.  
    
$ /usr/bin/cmake --build /home/rhodges/github/matthijs/websocket-test/cmake-build-debug-system-clang --target main  
   
[9/10] Building CXX object CMakeFiles/main.dir/websocket/variant_websocket.cpp.o  
FAILED: CMakeFiles/main.dir/websocket/variant_websocket.cpp.o   
/usr/bin/clang++ -DBOOST_ALL_DYN_LINK -DBOOST_ALL_NO_LIB -DBOOST_BEAST_USE_STD_STRING_VIEW -DBOOST_SYSTEM_DYN_LINK -DFMT_HEADER_ONLY -I/home/rhodges/github/matthijs/websocket-test/include -isystem /home/rhodges/work/libc++/include -stdlib=libc++ -march=native -fcoroutines-ts -g -std=c++17 -MD -MT CMakeFiles/main.dir/websocket/variant_websocket.cpp.o -MF CMakeFiles/main.dir/websocket/variant_websocket.cpp.o.d -o CMakeFiles/main.dir/websocket/variant_websocket.cpp.o -c /home/rhodges/github/matthijs/websocket-test/websocket/variant_websocket.cpp  
In file included from /home/rhodges/github/matthijs/websocket-test/websocket/variant_websocket.cpp:3:  
/home/rhodges/work/libc++/include/fmt/core.h:1717:3: error: static_assert failed due to requirement 'formattable' "Cannot format an argument. To make type T formattable provide a formatter<T> specialization: https://fmt.dev/latest/api.html#udt"  
  static_assert(  
  ^  
...  
/home/rhodges/github/matthijs/websocket-test/websocket/variant_websocket.cpp:108:10: note: in instantiation of function template specialization 'fmt::print<std::string, boost::beast::http::status>' requested here  
    fmt::print("weboscket::connect: response: {}, {}\n", ec.message(), response.result());  
         ^  
1 error generated.  
```

---

## Comment 5

> Username: matthijs  
> Created at: 2021-12-20 10:40:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-997805345  

You have the wrong repository, its https://github.com/matthijs/websocket-handshake

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-12-20 11:19:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-997834215  

oops. thanks.

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-12-20 12:03:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-997862560  

OK, I can see the problem. Unless the upgrade is a success, we don't actually copy the response object back to the supplied buffer.  
  
It think this is a bug. At the very least it's unfriendly.

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-12-20 15:39:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-998037019  

Confirmed. I have created a PR which runs a sync and async query simultaneously.  
The async one behaves as expected. The sync one does not fill in the response:  
  
```  
/home/rhodges/github/matthijs/websocket-handshake/cmake-build-debug-system-clang/main httpstat.us 443 blabla  
[async] The WebSocket handshake was declined by the remote peer  
[async] HTTP/1.1 401 Unauthorized  
Transfer-Encoding: chunked  
Content-Type: text/plain  
Server: Kestrel  
WWW-Authenticate: Basic realm="Fake Realm"  
Request-Context: appId=cid-v1:1e93d241-20e4-4513-bbd7-f452a16a5d69  
Strict-Transport-Security: max-age=2592000  
Set-Cookie: ARRAffinity=c8fcc30ceb9d725a7f379f8837c91f9e4afa35242bd4fb5dcad86baef02d41ad;Path=/;HttpOnly;Secure;Domain=httpstat.us:443  
Set-Cookie: ARRAffinitySameSite=c8fcc30ceb9d725a7f379f8837c91f9e4afa35242bd4fb5dcad86baef02d41ad;Path=/;HttpOnly;SameSite=None;Secure;Domain=httpstat.us:443  
Date: Mon, 20 Dec 2021 15:30:14 GMT  
  
10  
401 Unauthorized  
0  
  
  
[sync] The WebSocket handshake was declined by the remote peer  
[sync] HTTP/1.1 200 OK  
  
  
Process finished with exit code 0  
```

---

## Comment 9

> Username: matthijs  
> Created at: 2021-12-20 16:35:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-998088030  

Thats interesting. If I remember correctly I could also reproduce it with a co_await call like:  
```c++  
co_await ws.async_handshake(response, host, target, boost::asio::redirect_error(boost::asio::use_awaitable, ec));  
```  
I will check it again, maybe I remembered it incorrectly.

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-12-20 16:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-998092037  

See my PR https://github.com/matthijs/websocket-handshake/pull/1

---

## Comment 11

> Username: matthijs  
> Created at: 2021-12-20 20:36:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2364#issuecomment-998252012  

I've checked it again, you are right the async_handshake call behaves correctly.
