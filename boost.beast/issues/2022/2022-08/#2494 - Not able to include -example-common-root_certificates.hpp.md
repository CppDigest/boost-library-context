# #2494 - Not able to include "example/common/root_certificates.hpp" [Closed]

> Username: dpsingh28  
> Created at: 2022-08-08 04:05:25 UTC  
> Updated at: 2022-08-09 16:45:32 UTC  
> Closed at: 2022-08-09 16:45:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2494  

Hi  
I installed Boost 1.80.0 using the following instructions, [https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview](https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview), but I am not able to include the example root certificates in my node.  
  
I am working on a docker container with following specs:  
  
- Ubuntu 18.04  
- C++ 17  
  
  
I am aiming to setup an SSL websocket connection to query some data from a server. I am following this example: [https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp](https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp)  
  
**Things I have tried to resolve my issue:**  
- Specify Boost 1.80.0 in the CMakeLists.txt  
- export LD_LIBRARY_PATH environment variable to include the boost 1.80.0 installation directory  
  
  
**ERROR snap**:  
![image](https://user-images.githubusercontent.com/93106483/183336733-e4f4ce40-9682-46bc-99a2-e26a3f9ac5d8.png)  
  
  
Are there any other things I can try?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-08 13:40:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2494#issuecomment-1208146815  

root_certificates.hpp is part of the examples not part of the Beast library. You need to copy the file into your project.

---

## Comment 2

> Username: dpsingh28  
> Created at: 2022-08-09 16:45:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2494#issuecomment-1209622387  

Oh ok. Thanks, it worked!
