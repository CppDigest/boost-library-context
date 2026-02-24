# #189 - ssl alert are not being sent during handshake failure [Closed]

> Username: arvedarved  
> Created at: 2019-01-07 14:31:10 UTC  
> Updated at: 2019-03-13 16:53:10 UTC  
> Closed at: 2019-03-13 16:53:10 UTC  
> Url: https://github.com/boostorg/asio/issues/189  

Previously reported in TRAC: https://svn.boost.org/trac10/ticket/9261  
  
This issue can still be reproduced with recent versions of boost (tested with 1.67)  
  
It can be reproduced with the official examples from ssl/client.cpp ssl/server.cpp and wireshark.  
( https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/example/cpp03/ssl/client.cpp  
https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/example/cpp03/ssl/server.cpp )  
  
Or if you don't want to use wireshark:  
1. In client.cpp: Change the verify_certificate callback to always return false  
2. Add the following line to the handle_handshake method in server.cpp:  
`std::cerr << "Handshake Error: " << error.message() << std::endl;`  
On the server you will see `Handshake Error: stream truncated` printed, when using the example client, while when using curl a TLS Alert is displayed: `Handshake Error: tlsv1 alert unknown ca`

---

## Comment 1

> Username: arvedarved  
> Created at: 2019-03-13 16:53:10 UTC  
> Url: https://github.com/boostorg/asio/issues/189#issuecomment-472510178  

Fixed by the following commit: https://github.com/boostorg/asio/commit/2864d2bf32b06c380ee93f9b07301e3a2a11e2b8#diff-9693888572396784daf4f0aded26ce3a
