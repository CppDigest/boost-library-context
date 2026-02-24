# #2506 - certificate verify failed [Closed]

> Username: SC-One  
> Created at: 2022-08-23 18:42:58 UTC  
> Updated at: 2022-08-29 05:27:49 UTC  
> Closed at: 2022-08-29 05:27:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2506  

## Error on handshake (ssl)  
I got an error that I really dont know what can i do anymore , Error:  
``` handshake: certificate verify failed (SSL routines, tls_process_server_certificate) [asio.ssl:337047686] ```  
  
  
### Version of Beast :   
_BOOST_BEAST_VERSION 322_  
  
### Steps necessary to reproduce the problem  
built on android and work fine with http , just when I wanna use ssl and some custom handshake , I facing mentioned error.  
### All relevant compiler information  
clang-9  
(extra info , android api level 21 , arch abi: x86)  
  
#### Also tried:  
- [x] added custom path(pointing to CA folder in android devices) to verify path  
- [x] used default verify path  
- [x] changed verify method to verify_none , and it worked fine.  
- [x] changed verify callback to host_... and rfc28... , and my custom ...  
- [x] and some extra trying solutions.   
- [x] used [root_certificate](https://github.com/boostorg/beast/blob/develop/example/common/root_certificates.hpp) too (just for test)

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-08-23 18:56:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2506#issuecomment-1224623330  

Do other clients verify successfully? It looks as if the certificate is invalid?

---

## Comment 2

> Username: SC-One  
> Created at: 2022-08-23 19:02:42 UTC  
> Updated at: 2022-08-23 20:21:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2506#issuecomment-1224636008  

> Do other clients verify successfully? It looks as if the certificate is invalid?  
  
Yeah (this test is on google.com) , I tested on any website and it return **certificate verify failed**  
  
@madmongo1   
> Note: CA verify path in android has just *.0 files! , I don't know it's enough or not?   
  
[Ref code in ssl asio](https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/context.hpp#L339)

---

## Comment 3

> Username: SC-One  
> Created at: 2022-08-29 05:27:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2506#issuecomment-1229794171  

Ok I found it after wasting a lot of  days ...   
file name hashing was different in **android** vs **openssl**.   
  
@madmongo1 .
