# #249 - Websocket: send server initiated data to client [Closed]

> Username: hiharsh  
> Created at: 2017-02-03 17:22:29 UTC  
> Updated at: 2017-03-02 13:54:21 UTC  
> Closed at: 2017-03-02 13:54:20 UTC  
> Url: https://github.com/boostorg/beast/issues/249  

Hi, I would like o achieve following   
1) Start a websocket server on C++   
2) Listen for messages from client   
3)send response for messages based on some processing in my custom code.   
4) SEND SERVER INITIATED text message/string to client when something changes on my custom code.   
For # 1 to 3 I can use the echo server for 1 to 1 message handling (but that would be blocking to since I will be filling the response to the send string after I have processed the request from client).   
  
How do I achieve the above ? Any example code?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-03 17:49:09 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-277314534  

Sure, you could adapt the existing WebSocket asynchronous server example:  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_async_echo_server.hpp

---

## Comment 2

> Username: hiharsh  
> Created at: 2017-02-03 18:20:18 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-277322089  

Hi Vinniefalco,   
Thanks. I downloaded the repository and tried to run after performing cmake .. from example directory followed by make. It built the binaries but I dont find it doing anything.   
Here is the information.   
Did I miss any important steps ?   
  
user@VirtualBox:/media/sf_winshare/Beast-master/examples/exampl$ file websocket-echo   
websocket-echo: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=f931584b4051807c40dd1aee75d7a541067738ff, not stripped  
user@VirtualBox:/media/sf_winshare/Beast-master/examples/examples$ make  
[ 27%] Built target http-crawl  
[ 45%] Built target websocket-echo  
[ 63%] Built target http-server  
[ 81%] Built target websocket-example  
[100%] Built target http-example  
user@VirtualBox:/media/sf_winshare/Beast-master/examples/examples$ ./websocket-echo

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-02-03 18:22:58 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-277322709  

Well, the **websocket-echo** program listens on ports 6000 and 6001, you have to connect to it with a websocket client and send a message!  
See:  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_echo.cpp#L45  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_echo.cpp#L52  
  
You can follow the directions on this page for making a small JavaScript client program that runs in your browser:  
http://www.websocket.org/echo.html

---

## Comment 4

> Username: hiharsh  
> Created at: 2017-02-03 18:55:24 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-277330872  

Thanks,   
I was able to run the code and see the echo returning on the client itself with port 6001 ONLY.   
  
  
![image](https://cloud.githubusercontent.com/assets/18236397/22604132/2236981a-ea18-11e6-9772-9c97f016ba68.png)  
  
1) I think it would be good to show that data received on server side as well. Where do I get the data on server side? I will have to parse the string and act on it.   
2) I would like to send something from server and send it to client asynchronously.   
How do I do that?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-02-03 18:59:41 UTC  
> Updated at: 2017-02-03 19:00:54 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-277332020  

1. Did you have a chance to study the WebSocket asynchronous echo server example code? I can answer specific questions if you give me a link to a file and line. For example:  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_async_echo_server.hpp#L312  
  
2. Have you had a look at the documentation?  
  
There's some example code which shows how to send and receive messages:  
http://vinniefalco.github.io/beast/beast/websocket/messages.html  
  
You can also look at the reference for `beast::websocket::stream`:  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream.html  
  
Probably you're interested in `async_read` and `async_write`.

---

## Comment 6

> Username: hiharsh  
> Created at: 2017-02-03 19:36:29 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-277341378  

Let me admit that I am not boost or C++ expert.  This is very new to me so please bear me for any simple questions I ask.   
Based on the information you sent, I think I need async_read and async_write. What I was expecting from the library is simple calls to function to async_read and async_write which I can invoke from my custom C++ code.   
My question is very basic.   
1) Once the webSocket is up with the end point like 127.0.0.1:6001, if a client sends a message my code shall get the exact message that client send and act on it which could take a few seconds.   
I.e. once the code hits the line 312 in the link you posted   
[https://github.com/vinniefalco/Beast/blob/master/examples/websocket_async_echo_server.hpp#L312]  
  
2) once my custom code is done processing or some status changes on my custom code, I need to push that information in text format to client invoking async_write method.   
  
3) the client could send a new request upon receiving the async message from server. This should be handled the same as described in point #1 above.   
  
//pseudo code   
  
//Main   
//start webSocket   
//listen for incoming messages from client and upon reception pass the message to custom code    
//once custom code is done processing use async_write method to send the data to client.   
//keep doing above until connection is closed  
  
I dont't know how to call aysnc_write from my custom C++ code to as well pass text upon receiving client data to my custom code.   
  
I am trying to provide more information by dummy test class below.   
test.cpp   
class test  
{  
   public:   
  void processDataFromClient(const string & data);//this needs to get filled by websocket read method? how?   
  void pushDataToClient(const string & data);   
     
};  
test::pushDataToClient(const string & data)  
{  
  async_write(socketId, text) // how do I call this from my test.cpp?   
}  
  
 test::void processDataFromClient(const string & data)  
{  
   //do some computation based on the text received   
  //turn on the light   
  //once Light is on for a minute call   
    pushDataToClient( lighton) ;  
}  
  
//web_socket_server.cpp   
main   
{  
   //open a webSocket   
   //listen for messages from client //notify test class   
   //send messages to client  once async_write is called from test class ? //do we need a thread for this ? how to acheive this?   
     
}

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-02-03 19:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-277345309  

You could try using the synchronous interfaces, then you don't have to worry about threads or completion handlers. See:  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_sync_echo_server.hpp#L302

---

## Comment 8

> Username: hiharsh  
> Created at: 2017-02-03 20:10:51 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-277349807  

what does the synchronous interfaces do? or how does it work? Is there any .cpp file showing example of sync_echo_server functionality?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-02-08 15:10:10 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-278354626  

Yes:  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_echo.cpp#L48

---

## Comment 10

> Username: hiharsh  
> Created at: 2017-02-08 19:25:40 UTC  
> Updated at: 2017-02-08 19:26:23 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-278434294  

I commented out the line # 312 to 315 to disable echo to client (https://github.com/vinniefalco/Beast/blob/master/examples/websocket_sync_echo_server.hpp#L312  
  
I also attempted to create a thread to send data to client but I have not achieved it successfully since I can't figure out the correct way/syntax to pass socket/server instance to the thread. Kindly please let me know how do I do that.   
Here is my code now   
  
```  
#include "websocket_async_echo_server.hpp"  
#include "websocket_sync_echo_server.hpp"  
#include <boost/asio/io_service.hpp>  
#include <boost/asio/signal_set.hpp>  
#include <iostream>  
#include <boost/thread/thread.hpp>  
#include <unistd.h>  
#include <ctime>  
  
websocket::sync_echo_server s2{&std::cout}; //I thought creating this global might help  
/// Block until SIGINT or SIGTERM is received.  
void  
sig_wait()  
{  
    boost::asio::io_service ios;  
    boost::asio::signal_set signals(  
        ios, SIGINT, SIGTERM);  
    signals.async_wait(  
        [&](boost::system::error_code const&, int)  
        {  
        });  
    ios.run();  
}  
  
void testFunc()  
{  
#if 0  //beast::websocket::stream<boost::asio::ip::tcp::socket&> ws{sock};  
   beast::websocket::opcode op =1;  
   beast::streambuf sb = "Hello thread" ;  
   beast::error_code ec;  
   ws.set_option(beast::websocket::message_type{op});  
   ws.write(sb.data(),ec);  
  // s2.write(boost::asio::buffer(std::string("Hello, world!")));  
  // ws.write(boost::asio::buffer(std::string("Hello, world!")));  
#endif  
  
while(1)  
   {   
     std::cout << "Hello Thread " << std::endl;  
     sleep(1);  
   }  
}  
int main()   
{   
    using namespace beast::websocket;   
    using endpoint_type = boost::asio::ip::tcp::endpoint;   
    using address_type = boost::asio::ip::address;   
   beast::error_code ec;   
   permessage_deflate pmd;   
    pmd.client_enable = true;   
    pmd.server_enable = true;   
    pmd.compLevel = 3;   
//s2 is set global above  
    s2.set_option(read_message_max{64 * 1024 * 1024});   
    s2.set_option(auto_fragment{false});   
    s2.set_option(pmd);   
      boost::thread t1(&testFun);   
    // t1.join();  //why do I need this?   
     s2.open(endpoint_type{   
        address_type::from_string("127.0.0.1"), 8080 }, ec);   
    sig_wait();   
}  
```

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-03-02 13:54:20 UTC  
> Url: https://github.com/boostorg/beast/issues/249#issuecomment-283658974  

Hmm well I don't see any actionable issues here so I'm going to close this. If you encounter a bug or a missing feature please don't hesitate to open a new issue!
