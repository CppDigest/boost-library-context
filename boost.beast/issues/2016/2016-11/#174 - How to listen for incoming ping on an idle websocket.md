# #174 - How to listen for incoming ping on an idle websocket [Closed]

> Username: generationtech  
> Created at: 2016-11-08 03:57:22 UTC  
> Updated at: 2016-11-15 19:26:07 UTC  
> Closed at: 2016-11-08 18:21:04 UTC  
> Url: https://github.com/boostorg/beast/issues/174  

I have a beast websocket client connecting to django channels server. A lot of the time I want the websocket open but it's just left in an idle condition waiting for the client to have something to send. Periodically, the django channels server sends ping messages that the beast websocket does not reply to (because it's idle, not waiting to read or send). Shortly thereafter, the websocket is dropped.  
  
How can I have beast websocket listen and react to incoming periodic ping from server and reply with a pong when I'm not actually using the websocket for any current read or write operation? I understand from the docs, that when beast is actually doing something, it automatically handles the ping/pong process, but I need something to keep an idle connection alive

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-08 04:23:41 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259042231  

You have to have a call to `async_read` or `async_read_frame` pending

---

## Comment 2

> Username: generationtech  
> Created at: 2016-11-08 04:39:27 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259044103  

OK, to be clear, I can only have one async_read and/or async_write per websocket? Or for that matter, only one synchronous version?  
  
So in order to keep my websockets up, once handshake'd and connected, I would need to "hang" an async_read on each one to keep them alive. And later, whenever an incoming read actually happens (not a ping/pong), handle the read condition for my normal program purposes and then "re-hang" a new async_read.  
  
?  
  
Does that make sense?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-11-08 11:55:04 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259117885  

Yes. You can have one active read and one active write simultaneously. Sending a ping, pong, or close counts as a write. However, when Beast automatically responds to a ping with a pong, that does not count as a write. In order to receive pings, pongs, and close frames, you need to have a call to `async_read`, `async_read_frame`, `read`, or `read_frame` active. Pings and pongs received during this read will not cause the completion handler to be called, but the implementation will still handle the control frame for you. In the case of a pong, the pong callback will be called if it was set (using `set_option`). The read will complete only under these conditions:  
- An error occurs  
- A complete frame containing message data is received  
- The WebSocket stream undergoes a protocol close (exchange of close frames)

---

## Comment 4

> Username: generationtech  
> Created at: 2016-11-08 18:06:02 UTC  
> Updated at: 2016-11-08 18:08:09 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259212520  

Thanks for the help. Completely understand. For some reason, I'm having newbie kind of problem implementing the async_read. I've read through as many of examples and blog posts for Beast but cannot determine why this test of async_read does not work in my code (it never seems to receive an actual real message I send from server AND it does not handle the automatic ping/pong feature).  
  
```  
                    std::cout << "starting async_read" << "\n";  
                    wsm_client.async_read(wsm_op, wsm_sb,  
                        [&](boost::system::error_code const& ec)  
                    {  
                        std::cout << "read received" << "\n";  
                        std::cout << to_string(wsm_sb.data()) << "\n";  
                    });  
```  
  
**The full code of the watchdog process I have for now is:**  
  
void cl_WebsockManager::watchdog(void)  
{  
        try  
    {  
        boost::this_thread::sleep_for(boost::chrono::seconds{ WATCHDOG / (int)wsm_type });  
  
```  
    while (true)  
    {  
        boost::this_thread::sleep_for(boost::chrono::seconds{ WATCHDOG });  
  
        try  
        {  
            wsm_client.pong("pong");  
        }  
        catch (...)  
        {  
            wsm_flg_connected = false;  
        }  
  
        if (!wsm_flg_connected)  
        {  
  
            switch (wsm_type)  
            {  
            case websock_type::CLOUD:  
                if (connect(CLOUD_URL))  
                {  
                    std::cout << "starting async_read" << "\n";  
                    wsm_client.async_read(wsm_op, wsm_sb,  
                        [&](boost::system::error_code const& ec)  
                    {  
                        std::cout << "read received" << "\n";  
                        std::cout << to_string(wsm_sb.data()) << "\n";  
                    });  
  
                }  
                break;  
            case websock_type::GAME:  
                if (connect(GAME_URL))  
                {  
                    std::cout << "starting async_read" << "\n";  
                    wsm_client.async_read(wsm_op, wsm_sb,  
                        [&](boost::system::error_code const& ec)  
                    {  
                        std::cout << "read received" << "\n";  
                        std::cout << to_string(wsm_sb.data()) << "\n";  
                    });  
  
                }  
                break;  
            }  
        }  
    }  
}  
catch (boost::thread_interrupted&) {}  
```  
  
}  
  
**If I replace it with a sync read, it works all as expected:**  
  
```  
                    std::cout << "starting sync_read" << "\n";  
                    wsm_client.read(wsm_op, wsm_sb);  
                    std::cout << "read received" << "\n";  
                    std::cout << to_string(wsm_sb.data()) << "\n";  
```  
  
**The whole watchdog thread is instantiated like this in the class constructor:**  
  
wsm_watchdog = new boost::thread(boost::bind(&cl_WebsockManager::watchdog, this));

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-11-08 18:08:11 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259213101  

Are you calling `io_service::run()`?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-11-08 18:13:18 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259214503  

I need to see the complete program to have any chance of making a diagnosis

---

## Comment 7

> Username: generationtech  
> Created at: 2016-11-08 18:13:35 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259214567  

Not. I am very newbie to Beast and Boost, plus my C++ is pre v11 & v14, so I have much catching up to do, especially with Lambda's and Uniform Initialization.  
  
Where would I put the io_service::run() call? That watchdog thread runs as a continuous background process checking if the the websocket is still active and re-connecting when not.

---

## Comment 8

> Username: generationtech  
> Created at: 2016-11-08 18:15:35 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259215061  

The complete program is a Ball of Mess right now from converting from cpprestsdk to Beast/Boost. Basically, I'm implementing this chain of programs.  
  
ExistingGame<-->Assembly-CSharp<-->Pinvoke/ReversePinvoke<-->C++<-->BeastWebsocket<-->DjangoChannels<-->Django

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-11-08 18:17:52 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259215659  

In order to get the most out of Beast, you really need to start with an understanding of Boost.Asio. And understanding the role of the `io_service` and its associated threads is a requirement for doing asynchronous I/O successfully. Here's a starting point:  
http://www.gamedev.net/blog/950/entry-2249317-a-guide-to-getting-started-with-boostasio/  
  
I would look at each of the tutorials here:  
http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/tutorial.html  
  
You can also study the asynchronous echo server example in Beast to get some ideas on best practices for building a WebSocket server:  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/websocket_async_echo_server.hpp

---

## Comment 10

> Username: generationtech  
> Created at: 2016-11-08 18:19:50 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259216142  

Ok thanks for the links. I'll Be Back!

---

## Comment 11

> Username: vinniefalco  
> Created at: 2016-11-08 18:20:31 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259216325  

It looks like there are many things missing, and incorrect with your program. You need to perform a websocket handshake before sending and receiving messages (using `beast::websocket::stream::async_accept` or `beast::websocket::stream::async_handshake`)  
  
You need to have an instance of `boost::asio::io_service`, and you need to have at least one thread calling `io_service::run` on that object. Since this is your first async program, I would go with just the one thread (or else you have to otherwise protect sockets using a "strand").  
  
And you need to make sure that you access these sockets and stream objects in a thread safe way. You cannot call members like `async_read` from more than one thread at once, or else behavior is undefined.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2016-11-08 18:21:04 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259216465  

I'm going to go ahead and close this issue for now, once you've brushed up if you are still have trouble we can open a new issue. Thanks!

---

## Comment 13

> Username: generationtech  
> Created at: 2016-11-08 18:22:35 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259216928  

**Just to be clear, the connect function does the setup.**  
  
bool cl_WebsockManager::connect(std::string sock_url)  
{  
    std::string srv_url;  
  
```  
srv_url += URL_PREFIX;  
srv_url += sock_url;  
srv_url += "/";  
srv_url += wsm_server_key;  
  
try  
{  
    boost::asio::connect(wsm_sock, wsm_r.resolve(boost::asio::ip::tcp::resolver::query{ SERVER_URL, "80" }));  
}  
catch (...)  
{  
    wsm_flg_connected = false;  
    return wsm_flg_connected;  
}  
  
try  
{  
    wsm_client.handshake(SERVER_URL, srv_url);  
    wsm_flg_connected = true;  
}  
catch (...)  
{  
    wsm_flg_connected = false;  
    return wsm_flg_connected;  
}  
  
return wsm_flg_connected;  
```  
  
}

---

## Comment 14

> Username: vinniefalco  
> Created at: 2016-11-08 18:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259217204  

That looks correct for a client. Note though, that you are mixing synchronous and asynchronous code. Which is okay as long as you understand the implications.

---

## Comment 15

> Username: generationtech  
> Created at: 2016-11-08 18:28:18 UTC  
> Updated at: 2016-11-08 18:28:59 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259218470  

Yes, generally I do. This whole background process, mutex, thread safe, and such I understand for many years before starting this project. It's the C++ and new-to-me Boost that I need to update myself.  
  
For the connect function, I wanted to immediately catch the exception to see if the server was not available at that moment. I figured since the overall watchdog function was running as a background thread anyway that blocking with the connect as a sync function was ok. It was only when I encountered the need for continuous ping/pong monitoring of an idle websocket did I have to introduce the "hanging" async_read call and could not use sync there because it blocked the watchdog. All of that seemed to be previously handled automatically with cpprestsdk just by bringing up the websocket alone.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2016-11-08 18:30:41 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259219134  

What if someone wants to exit your program immediately? That's not going to be possible with synchronous APIs, because they lack a timeout mechanism. Consider the case where a remote server is overloaded. It connects, but takes a minute to send the handshake response. Using a synchronous API, your program cannot be cleanly exited until the synchronous operation times out completely. And you will have no control over the duration of that timeout.

---

## Comment 17

> Username: generationtech  
> Created at: 2016-11-08 18:39:44 UTC  
> Updated at: 2016-11-08 18:44:35 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-259221533  

You are of course right and I imagine I'd have to implement a more refined approach as I better understood the library in general.  
  
But right now, if the program aborts or exits without cleaning itself up, on the django channels side, it's no problem and always immediately seems to know the connection has dropped. On that end, I store the websocket circuit id in a mysql database so the django server knows how to contact the game server when/if needed later. This whole system will eventually connect to 1,000s of existing game servers.  
  
The basic design is the game server needs to talk to the cloud app as needed, and the cloud app to talk to the game server for a different purpose at a different time. Yes, I know websockets are bi-directional, but I didn't want to deal with mulitplexing incoming & outgoing purposes through just one of them. By using 2 websockets, one for incoming purpose, and the other for outgoing purpose, I am certain what is happening in my actual program logic. Of course, the naming of these as "incoming" or "outgoing" websockets is arbitrary on my end; both of them are receiving or sending as needed when they are processing a given command or verb. Like I said, I didn't want to deal with multiplexing purposes through a single websocket.

---

## Comment 18

> Username: generationtech  
> Created at: 2016-11-15 19:26:06 UTC  
> Url: https://github.com/boostorg/beast/issues/174#issuecomment-260740564  

Finally got around to reading all those docs. I feel like Neo in The Matrix. "I know kung fu."  
  
I'm pressing on with my beast websocket implementation. Surely I'll get stuck again.
