# #1830 - Unable to move socket out from a non-owning websocket stream [Closed]

> Username: ghost  
> Created at: 2020-01-30 07:02:54 UTC  
> Updated at: 2020-03-10 03:49:03 UTC  
> Closed at: 2020-03-10 03:49:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1830  

Hi Vinnie,  
  
I have created a wrapper class for a websocket that stores a tcp socket and a non-owning websocket stream member, and provides a move constructor as follows.  
  
```  
class WebSocket  
{  
public:  
  
  // ...  
    
  WebSocket(WebSocket&& other)  
      : m_socket(std::move(other.m_socket))  
      , m_stream(m_socket) { }  
  
  ~WebSocket() = default;  
  
  // ...  
  
private:  
  tcp::socket m_socket;  
  beast::websocket::stream<tcp::socket&> m_stream;  
};  
```  
  
If I begin with a websocket wrapper instance, establish a connection and accept the handshake, then I can use the stream to send and receive messages as expected. However, if I move-construct a new websocket wrapper from an existing instance, any attemp to read or write on the new instance results in an "Operation Canceled" error.  
  
I dont understand where the cancelled state is coming from. As the websocket stream is non-owning, I would assume that it has no way to modify the state of the underlying socket after it has been moved out from under it. Is what I am trying to do possible?  
  
I assume this is a misunderstanding on my behalf rather than a bug, but I do have a minimal code sample to demonstrate the issue using straight boost/beast objects if it helps at all. I'll note that I am using the synchronous API, and this is Beast version 277.  
  
I appreciate any help you can offer!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-30 12:44:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580235704  

Hi @unl1ght,  
  
Thanks for getting in touch.  
  
I wouldn't expect this operation to work. The moved-to websocket would have no way of knowing the address of the moved-to socket.  
  
I you wouldn't mind telling me what outcome you are looking to achieve, I'd be happy to advise on an approach that fits within the beast model.

---

## Comment 2

> Username: ghost  
> Created at: 2020-01-30 22:22:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580489901  

Hi @madmongo1,  
  
Thank you for the response! Are you saying that the socket address information is not available from the moved-to socket? Would this be due to the handshake occuring in the previous websockt stream?  
  
Ultimately I have created a collection of network protocol wrappers that all share a common base class that holds the underlying socket and provides some common functions, including a move constructor. I was hoping to use the same base class for the websocket. My cunning plan was to take advantage of the non-owning template option so that the underlying socket could still live in the common base class, and thats where the move constructor issue comes in. The following sample is closer to what I am trying to do.  
  
```  
template <typename T_Socket>  
class SocketBase  
{  
  SocketBase(SocketBase&& other) : m_socket(std::move(other.m_socket)) { }  
    
  // Common socket functions..  
  
protected:  
  T_Socket m_socket;  
};  
  
class UdpSocket : public SocketBase<udp::socket>  
{  
  // UDP specific funcitons..  
};  
  
class WebSocket : public SocketBase<tcp::socket>  
{  
  Websocket(Websocket&& other)  
      : SocketBase(std::move(other))  
      , m_stream(m_socket) { }  
    
  // Websocket specific funcitons..  
  
private:  
  beast::websocket::stream<tcp::socket&> m_stream;  
};  
  
```  
  
Do you have any advice on this plan? I guess it might not be suitable to use the base class for the websocket as the API is more specific than a lot of the other protocols. I appreciate your time!

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-01-30 23:37:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580512691  

What I am saying is a little more fundamental.  
  
You are creating a websocket that intends to hold a reference to a socket:  
  
`beast::websocket::stream<tcp::socket  /* here */ & /* here */  > m_stream`  
  
> However, if I move-construct a new websocket wrapper from an existing instance  
  
If you construct m_stream and then the socket it is referring to moves (changes memory address). How will the websocket know what the new address of the socket is? It will be holding a reference to a moved-from socket, i.e. a dead lifeless husk of a socket.  
  
As I mentioned, it's probably better to discuss the outcome you would like to achieve rather than how to make the code above work.  
  
What is the high-level aim here?

---

## Comment 4

> Username: ghost  
> Created at: 2020-01-31 00:30:33 UTC  
> Updated at: 2020-01-31 00:58:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580526412  

> If you construct m_stream and then the socket it is referring to moves (changes memory address). How will the websocket know what the new address of the socket is? It will be holding a reference to a moved-from socket  
  
This isn't exactly my issue, as I am creating a new websocket stream around the moved-to socket, not using the old stream with moved-from socket. I apologise for posting another code sample, but I feel this shows the issue more clearly. At a higher level, I am trying to accept websocket sessions in a server thread, then move the underlying tcp socket to another thread, to be rewrapped and used to send/receive messages, but doing this produces an error.  
  
```  
void runSession(tcp::socket socket)  
{  
  beast::websocket::stream<tcp::socket&> stream(socket);  
  boost::system::error_code error;  
  beast::flat_buffer buffer;  
  
  if (stream.read(buffer, error); error)  
  {  
    std::cout << error.message() << std::endl;  // <<<  ERROR: Operation canceled  
  }  
}  
  
void runServer(net::io_context& context, tcp::endpoint endpoint)  
{  
  tcp::acceptor acceptor(context, endpoint);  
    
  while (true)  
  {  
    tcp::socket socket(context);  
    acceptor.accept(socket);  
  
    beast::websocket::stream<tcp::socket&> stream(socket);  
    stream.accept();  
  
    std::thread(runSession, std::move(socket)).detach();  
  }  
}  
```  
  
In this case I don't understand how the websocket in the session thread is getting cancelled.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-01-31 07:59:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580626744  

I don't know if this code sample is real code from your application or not. One thing I would say is that I would suggest embedding the socket into the new websocket, like this:  
  
```  
    // note: no & in the template argument  
    beast::websocket::stream<tcp::socket> stream(std::move(socket));  
```  
  
Otherwise you run the risk of the socket being destructed (and therefore closed, which would cause an `operation_aborted` error as observed).  
  
I actually don't think the websocket::stream is designed to support a reference type as the `NextLayer` type. The docs don't make it clear, so I'll check it with @vinniefalco and either eat my words or create an issue to get the documentation clarified.  
  
In the meantime, the change above should go a long way to solving your problem.  
  
If not, I would suggest you create a small program on github to demonstrate the issue and link it here. Then I can compile and run it to confirm exactly what is wrong and how to fix it.

---

## Comment 6

> Username: ghost  
> Created at: 2020-01-31 11:56:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580704894  

Hi mate,  
  
> I actually don't think the websocket::stream is designed to support a reference type as the NextLayer type.  
  
I did read that the reference type was supported in the [documentation](https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_websocket/creating_streams.html) but only just realised that the docs are for a previous version of beast. Would you be able to confirm if this is in-fact not supported anymore?  
  
> Otherwise you run the risk of the socket being destructed (and therefore closed, which would cause an operation_aborted error as observed).  
  
The error I am getting is specifically an operation canceled error, which is different from the operation aborted error type.  
  
> I don't know if this code sample is real code from your application or not.  
  
That last code sample was straight boost beast code, using the namespace aliases from the beast example programs.  
  
If it turns out this feature is not supported and was removed from the documentation accordingly then I will be happy to use the method that you provided above. But if the library does support non-owning streams then I feel that the move constructor usage in my previous code sample is logical and I might just be missing something.  
  
Thank you for being patient with me here.

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-01-31 12:13:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580710128  

Hi @unl1ght. Please don't worry. It's part of my work to support users of the library.  
  
Which version of beast are you using?  
  
If you'd like to join me on slack to make communication easier, you're welcome to.  
  
https://cpplang.slack.com/archives/CD7BDP8AX

---

## Comment 8

> Username: ghost  
> Created at: 2020-01-31 13:03:28 UTC  
> Updated at: 2020-01-31 13:04:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580724928  

I am using Beast version 277.  
  
Thank you for the Slack invite!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-01-31 13:43:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580738334  

The reference should work

---

## Comment 10

> Username: ghost  
> Created at: 2020-01-31 23:05:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-580949524  

In this case I believe the move construction should work. I have attached a minimal, self contained program that demonstrates the problem. If you run this program and attempt to connect to it using the [websocket chat client](https://www.boost.org/doc/libs/1_72_0/libs/beast/example/websocket/server/chat-multi/chat_client.html) html page provided in the beast examples then you will see the error occur.  
  
```  
#include <boost/beast.hpp>  
#include <boost/asio.hpp>  
#include <iostream>  
  
namespace beast = boost::beast;  
namespace net = boost::asio;  
using tcp = net::ip::tcp;  
  
const char* host = "127.0.0.1";  
const uint16_t port = 8080;  
  
void doRead(tcp::socket socket)  
{  
  beast::websocket::stream<tcp::socket&> stream(socket);  
  boost::system::error_code error;  
  beast::flat_buffer buffer;  
  stream.read(buffer, error);  
    
  std::cout << "Error: " << error << " Message: " << error.message() << std::endl;  
}  
  
void doAccept(net::io_context& context, tcp::endpoint endpoint)  
{  
  tcp::acceptor acceptor(context, endpoint);  
  tcp::socket socket(context);  
  
  std::cout << "Waiting for connection.." << std::endl;  
  acceptor.accept(socket);  
  
  beast::websocket::stream<tcp::socket&> stream(socket);  
  stream.accept();  
  
  doRead(std::move(socket));  
}  
  
int main()  
{  
  tcp::endpoint endpoint(net::ip::make_address(host), port);  
  net::io_context context;  
  doAccept(context, endpoint);  
}  
```

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-02-01 13:20:50 UTC  
> Updated at: 2020-02-01 13:21:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-581029733  

Hi @unl1ght. Many thanks for providing a complete example. This has led me directly to the source of the problem.  
  
The problem is that you are accepting (performing a websocket handshake) on one websocket and then attempting to receive data on another. This is not possible as a `websocket::stream` object is stateful. It contains data buffers, timers and a fair amount of state information (the websocket protocol is not trivial).  
  
You must use the *same* websocket stream object to communicate as you used to accept. Note that the websocket stream does support `std::move`, as you will see in my repo.  
  
I have prepared a git repo to demonstrate one solution. There is a directory called `wrong` which contains a copy of your program and a directory called correct which contains one possible corrected version.  
  
Here is link to the repo: https://github.com/test-scenarios/beast-issue-1830  
  
Regards,  
  
Richard

---

## Comment 12

> Username: ghost  
> Created at: 2020-02-01 22:25:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-581075624  

Hi @madmongo1,  
  
That seems super obvious now that you have explained it, I thought that it must have been some oversight on my part.  
  
Thank you so much for looking into this for me!

---

## Comment 13

> Username: stale[bot]  
> Created at: 2020-03-03 02:59:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-593741633  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: stale[bot]  
> Created at: 2020-03-10 03:49:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1830#issuecomment-596888024  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
