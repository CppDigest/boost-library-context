# #206 - HTTP SSL Server and WebSocket SSL Server Examples [Closed]

> Username: jmin911  
> Created at: 2016-12-12 15:14:04 UTC  
> Updated at: 2017-06-21 01:05:55 UTC  
> Closed at: 2017-06-21 01:05:55 UTC  
> Url: https://github.com/boostorg/beast/issues/206  

Very good and easy to construct HTTPS and WebSocket SSL client side.  
However, I am having difficulty making the HTTPS and WebSocket SSL server side work.  Would be great if there are examples that show HTTPS and WebSocket SSL server code.  
SSL is very important as nowadays, everything needs to be communicated in secure channels.  
Thanks,

---

## Comment 1

> Username: rberlich  
> Created at: 2017-01-05 19:15:15 UTC  
> Updated at: 2017-01-05 19:16:06 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-270731256  

There is a comprehensive echo websocket server example under test/websocket/websocket_async_echo_server.hpp . Any chance this gets promoted to an example rather than a test? Or, more preferably, ist there a chance of a websocket server example accepting std::function<> arguments for the likes of "onopen", "onmessage", "onerror" and "onclose" ? I suppose that this is what most people intending to build a protocol exchange on top of Beast would be looking for.  
  
Thanks and a happy new year to you all,  
Beet

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-01-05 19:29:33 UTC  
> Updated at: 2017-01-05 19:30:25 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-270734660  

>Any chance this gets promoted to an example rather than a test  
  
Yeah, that's on my to-do. The reason it is still a test, is because it has some extra code in it to facilitate the unit tests. Code like this:  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/websocket_async_echo_server.hpp#L234  
  
> [is] there a chance of a websocket server example accepting std::function<> arguments for the likes of "onopen", "onmessage", "onerror" and "onclose"  
  
I want to do something like this, build a generic server that users can instantiate and customize. But its difficult to do so in a way that is sufficiently general purpose as to merit inclusion in the public interfaces.

---

## Comment 3

> Username: rberlich  
> Created at: 2017-01-06 20:04:53 UTC  
> Updated at: 2017-01-06 20:09:25 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-270992893  

Hi Vinnie, o.k., I take it that for everyday usage the "RAW" section of operator() in the above test is irrelevant. Do I understand it correctly that all "payload / message" data is received in /* else if(match(d.db, "TEXT")) */ ? What about binary frames ? Essentially I want to exchange some serialized objects between clients and a server, following a simple, custom protocol. I can of course uuencode everything, but would prefer to be able to "just" send Boost.Serialize-data, either in binary or XML format.  Thanks, Beet

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-01-06 20:09:36 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-270993927  

That is correct, all of that "RAW", "TEXT', etc.. are custom message payloads to facilitate testing. The value `d.op` (or `d_->op`) indicates whether the incoming payload is binary or text. To set the outgoing message type use `stream::set_option`:  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/set_option/overload5.html http://vinniefalco.github.io/beast/beast/ref/websocket__message_type.html

---

## Comment 5

> Username: rberlich  
> Created at: 2017-01-06 20:10:16 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-270994065  

O.k., thanks. Kind Regards, Beet

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-01-30 17:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-276124787  

@rberlich The change to make the servers part of the examples will go into **1.0.0-b25**, thanks!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-05-08 23:29:22 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-300019086  

There's a websocket ssl server now

---

## Comment 8

> Username: vinhemotiv  
> Created at: 2017-06-03 03:49:58 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-305948773  

hi Vinni,  
 "There's a websocket ssl server now ? "  
Where can i take a look on the Examples code of ssl server?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-03 04:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-305951504  

https://github.com/vinniefalco/Beast/blob/master/test/websocket/ssl/websocket_async_ssl_echo_server.hpp

---

## Comment 10

> Username: vinhemotiv  
> Created at: 2017-06-05 11:25:23 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-306165490  

Hi Vinnie,   
when i build your examples code. I always throw and exception. Look like echo ssl server is not working yet ???  
 try  
        {  
            for(;;)  
            {  
                ws.read(op, b);  
                b.consume(b.size());  
            }  
        }  
        catch(system_error const& se)  
        {  
            if(se.code() != beast::websocket::error::closed)  
                throw;  
        }  
        BEAST_EXPECT(boost::lexical_cast<std::string>(  
            buffers(b.data())) == "Hello, world!");

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-06-05 11:31:17 UTC  
> Updated at: 2017-06-05 11:32:28 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-306166405  

I don't know why that's not working for you. The asynchronous echo server runs as part of the tests, and it has never thrown an exception on my machine or on the numerous Travis CI matrix configuratons:  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/ssl/ssl_server.cpp#L131  
  
Maybe you can print the value of `se.what()`? Where is the exception being thrown? I'm going to need more information in order to help.

---

## Comment 12

> Username: vinhemotiv  
> Created at: 2017-06-05 11:36:52 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-306167267  

I was able to build your all projects, examples, and test with Visual Studito.   
How can i run test ?   
        server.open(endpoint_type{  
            address_type::from_string("127.0.0.1"), 6000 }, ec); => open port 6000  
And handshake at what port ?          
beast::websocket::stream<stream_type&> ws{stream};  
        ws.handshake("localhost", "/");  
  
Thank alot for your help  
Vinh

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-06-05 11:44:01 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-306168413  

Please use the Github flavored markdown to format code (triple backquote blocks).  
  
There are some problems with the code you gave me. Where are you making the connection? What is `stream_type`?

---

## Comment 14

> Username: vinhemotiv  
> Created at: 2017-06-05 11:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-306169678  

https://github.com/vinniefalco/Beast/blob/master/test/websocket/ssl/ssl_server.cpp  
  
I just built everything from your code. Don't change anything.  
![exception](https://cloud.githubusercontent.com/assets/13267003/26782943/fe389fcc-4a1f-11e7-91ce-82172e3a3ca9.PNG)

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-05 12:27:47 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-306175561  

I don't see the value of `se.what()`

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-06-08 20:08:46 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-307213193  

@jmin911 @vinhemotiv Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!

---

## Comment 17

> Username: rberlich  
> Created at: 2017-06-09 08:37:12 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-307330600  

Dear Vinnie,  
  
I plan to participate, as promised.  
  
Kind Regards,  
Ruediger  
  
> Am 08.06.2017 um 22:08 schrieb Vinnie Falco <notifications@github.com>:  
>   
> @jmin911 <https://github.com/jmin911> @vinhemotiv <https://github.com/vinhemotiv> Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost <https://lists.boost.org/mailman/listinfo.cgi/boost>) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub <https://github.com/vinniefalco/Beast/issues/206#issuecomment-307213193>, or mute the thread <https://github.com/notifications/unsubscribe-auth/ASlWXyeYyfAs63ttI1rU_5BkFn1ijdyPks5sCFTQgaJpZM4LKpyt>.  
>

---

## Comment 18

> Username: vinhemotiv  
> Created at: 2017-06-13 08:18:01 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-308042549  

Dear Vin, I will participate.

---

## Comment 19

> Username: vinhemotiv  
> Created at: 2017-06-13 08:22:41 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-308043658  

Btw, i have another question. How can Beast can handle a Websoket in CLOSE_WAIT status ?  
There are some cases that client apps couldn't  close websocket correctly. So i want to handle that case in order to force to close websocket.  
Best regards,  
Vinh

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-06-13 11:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-308092015  

I'm not sure I understand the question. How do you want CLOSE_WAIT to be handled?

---

## Comment 21

> Username: vinniefalco  
> Created at: 2017-06-21 01:05:55 UTC  
> Url: https://github.com/boostorg/beast/issues/206#issuecomment-309933488  

This is done in server-framework.  
  
@vinhemotiv You can use the code in `example/server-framework` it handles closing connections correctly in all cases.
