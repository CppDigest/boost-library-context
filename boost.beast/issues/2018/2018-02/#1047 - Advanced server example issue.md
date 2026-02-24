# #1047 - Advanced server example issue [Closed]

> Username: treebeard-66  
> Created at: 2018-02-27 15:54:50 UTC  
> Updated at: 2018-03-01 15:58:55 UTC  
> Closed at: 2018-03-01 15:58:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1047  

Hey There  
   
I'm not sure if this is a bug but I'm totally stumped...  
  
I've copied the advanced server example code and pasted it into visual studio 2015, linking against Boost 1.66 (BOOST_BEAST_VERSION 144) and this is the only code in the project.  
http://www.boost.org/doc/libs/develop/libs/beast/example/advanced/server/advanced_server.cpp  
  
I write socket client index.html file containing only the example code here: https://www.tutorialspoint.com/html5/html5_websocket.htm  
  
And it connects and echos the message. I do nothing and then after ~5 seconds I get a read access violation in beast/websocket/impl/read.impl at line 323 ('goto loop').   
  
Am I doing something stupid?  
  
Cheers

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-27 16:37:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-368940980  

You don't seem to be doing anything wrong. I tried your scenario using the latest Visual Studio and Boost 1.66.0 and did not have a problem. I modified the **html5_websocket.htm** script to connect to `localhost:8080` (where my advanced server is running) and had no trouble.  
  
Could you please try this in the latest Visual Studio to determine if the problem is Visual Studio 2015?

---

## Comment 2

> Username: carolinebeltran  
> Created at: 2018-02-27 22:22:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369047929  

treebeard-66, I got the same behavior on **VC2015 32bit** & **VC2017 64bit**.  On **GCC 6.3 64bit**, it did not compile, but then I noticed that the link you sent appears to be an **older version of the demo**.  I tried this one:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/example/advanced/server/advanced_server.cpp  
  
The new demo does not produce the access violation that you mentioned and appears to work just fine to me.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-27 23:22:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369063367  

> the link you sent appears to be an older version of the demo  
  
This is the most recent version, and what will appear in the upcoming Boost 1.67.0 release:  
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp  
  
This file should be the same as the one above  
http://www.boost.org/doc/libs/develop/libs/beast/example/advanced/server/advanced_server.cpp  
  
This is what is currently shipping in Boost 1.66.0:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/example/advanced/server/advanced_server.cpp  
  
Are you saying that the 1.66.0 version succeeds, but the newer one fails?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-02-27 23:38:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369066877  

The tip of **develop** works correctly for me, I cannot reproduce this

---

## Comment 5

> Username: carolinebeltran  
> Created at: 2018-02-28 04:47:24 UTC  
> Updated at: 2018-02-28 04:50:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369120509  

Hello Vinnie,   
  
The link posted by treebeard-66 and the github link you posted are identical.  I had assumed that it was older code since I'm new here.  Nevertheless, with this demo, I experienced the same access violation as treebeard-66 when using **VC++ 2015 32bit** and **VC++ 2017 64bit**.  When using **GCC 6.3.0**, I get a compiler error.  These are the only 3 compilers that I have access to.  
  
Please note that I am still on **Boost 1.66.0**, and that I have not updated any Boost library code.  This is the error produced by the **github version** on **GCC 6.3.0**:  
  
`main.cpp:237:39: error: no matching function for call to ‘boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::control_callback(std::_Bind_helper<false, void (websocket_session::*)(boost::beast::websocket::frame_type, boost::basic_string_view<char, std::char_traits<char> >), websocket_session*, const std::_Placeholder<1>&, const std::_Placeholder<2>&>::type)’`  
  
P.S.  For Ubuntu, I built Boost 1.66.0 from source and for both VC++ versions, I downloaded the pre-compiled versions of Boost 1.66.0.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-02-28 14:47:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369261824  

The advanced-server example has no file named main.cpp

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-02-28 14:50:40 UTC  
> Updated at: 2018-02-28 14:50:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369262953  

>This is the error produced by the github version on GCC 6.3.0:  
  
The signature for `stream::control_callback` was changed after 1.66.0, so a newer advanced_server.cpp will not compile with the Beast from 1.66.0. MSVC allows lvalues to bind to non-const references as parameters, which is a Microsoft extension and explains why it compiles for you there but causes an error in gcc.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-02-28 14:53:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369263899  

>Am I doing something stupid?  
  
I think I figured it out. You are trying to use the newer version of advanced_server.cpp with the older version of Beast. And the control_callback function signature was changed, so a crash is likely. If you are using Boost 1.66.0, please use the advanced_server.cpp from the Boost 1.66.0 release, which can be found here:  
  
https://github.com/boostorg/beast/blob/boost-1.66.0/example/advanced/server/advanced_server.cpp  
  
Thanks!

---

## Comment 9

> Username: carolinebeltran  
> Created at: 2018-02-28 18:17:00 UTC  
> Updated at: 2018-02-28 18:17:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369331997  

> The advanced-server example has no file named main.cpp  
  
Oh, I'm really sorry about causing confusion with the **main.cpp** error message.  I totally forgot to mention that I renamed the source file.  I do that because I have some blank 'Beast' template projects that I paste your demo code into in order to avoid setting any compiler options.  I'll keep that in mind from now on to avoid this.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-03-01 11:51:53 UTC  
> Updated at: 2018-03-01 11:52:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369568310  

>I think these issues are a me problem. Any ideas would be greatly appreciated though.  
  
As I said, the latest version of the advanced server example does not compile against Boost 1.66, as the signature for `stream::control_callback` has changed. If you want to compile the advanced-server example against Boost 1.66 then you need to use the version of the example which actually shipped in 1.66. That file can be obtained here:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/example/advanced/server/advanced_server.cpp

---

## Comment 11

> Username: treebeard-66  
> Created at: 2018-03-01 11:57:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1047#issuecomment-369569638  

Hey Vinnie. Yeah I deleted that message as saw the conversation i missed. Thanks, that works perfectly now. Can't believe I did that. Cheers mate
