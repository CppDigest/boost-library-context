# #1058 - WebSocket stream reads incorrect message. [Closed]

> Username: JunielKatarn  
> Created at: 2018-03-06 10:11:03 UTC  
> Updated at: 2018-03-07 02:42:41 UTC  
> Closed at: 2018-03-07 02:42:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1058  

For reference, I created this GitHub project as proof of concept:  
https://github.com/JunielKatarn/BeastSample  
  
### Version of Beast  
144  
  
### Steps necessary to reproduce the problem  
(Build and dependency setup detailed in the `README.md` file).  
1. Start the Node WebSocket server.  
1. Run WebSocketSample.exe (client).  
  
The expected client output is `[SUCCESS]`, which happens when the correct string was read from the server: `MESSAGE_FROM_SERVER`.  
The obtained message is `Connected! :D`.  
  
### All relevant compiler information  
- This project is built using the NuGet packages for Boost, distributed by Sergey Shandar at https://www.nuget.org/packages/boost/.  
- The project is Windows-only, and requires Microsoft Visual C++ 2017.  
- The following boost-related compiler flags are set:  
  - `BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE`  
  - `_WINSOCK_DEPRECATED_NO_WARNINGS`  
  - `_WIN32_WINNT=_WIN32_WINNT_WIN10`  
  
### Details  
See this line in Server.js:  
`ws.send('Connected! :D');`  
The statement above happens after a successful connection on the server side.  
If removed, the correct message goes through when the client submits a text string.  
Note, I tried the same workflow using a JavaScript WebSocket in the Google Chrome developer console, and it doesn't show this behavior.  
Sample code ran in the Chrome Browser:  
```  
var ws = new WebSocket('ws://localhost:5000/');  
ws.onmessage = function(e) { console.log(e.data); };  
ws.send('hola');  
MESSAGE_FROM_SERVER  
```  
  
Is there any specific setup to be made so such messages sent at connection time are discarded?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-06 12:22:51 UTC  
> Updated at: 2018-03-06 12:23:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1058#issuecomment-370764483  

I don't see any bug here. The server is clearly sending a WebSocket message with the  
contents "Connected! :D", as this line indicates:  
https://github.com/JunielKatarn/BeastSample/blob/b4056c1301feb95d9e56cc7f94d4b24bc398bf35/Server.js#L17  
  
Your client only performs one read:  
https://github.com/JunielKatarn/BeastSample/blob/b4056c1301feb95d9e56cc7f94d4b24bc398bf35/WebSocketSample/Client.cpp#L45  
  
>Is there any specific setup to be made so such messages sent at connection time are discarded?  
  
Your client needs to keep reading messages until it sees the error code `boost::beast::websocket::error::closed` indicating that the connection is closed. Or it needs to keep reading messages until some other condition is met, such as receiving a message containing the text "MESSAGE_FROM_SERVER". Your program only does one read and then closes. The server sends two messages. So the program will only ever see the first message, just as you described.  
  
I suggest you try starting from one of the Beast example WebSocket clients (such as websocket-async) and modify it to suit your needs.

---

## Comment 2

> Username: JunielKatarn  
> Created at: 2018-03-07 01:07:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1058#issuecomment-370986297  

Noted.  
  
The issue I'm facing is, the servers I will connect to may or may not send a message on connect (I have no control over that).  
  
I need the client to only handle messages received AFTER it starts sending.  
However, if I make an async read right after the handshake, and the server does not send a message right after connecting, the application hangs (see updated sources for reference).  
  
Is there a way to configure the WebSocket stream or the IO context to only accept messages after a specific event, or to expire a hung read operation?  
  
Thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-03-07 02:41:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1058#issuecomment-371003253  

>Is there a way to configure the WebSocket stream or the IO context to only accept messages after a specific event, or to expire a hung read operation?  
  
No, you have to do that yourself by adding the appropriate logic to your program.

---

## Comment 4

> Username: JunielKatarn  
> Created at: 2018-03-07 02:42:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1058#issuecomment-371003376  

Alright. There is no issue related to Beast, then.
