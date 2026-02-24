# #1871 - Async websocket client ignores target value [Closed]

> Username: AnwaPeck  
> Created at: 2020-03-05 14:09:53 UTC  
> Updated at: 2020-04-10 10:48:26 UTC  
> Closed at: 2020-04-10 10:48:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1871  

The async websocket client ALWAYS reaches the root "/" endpoint, regardless of target value "/foo" that I specify.  Do I need to specify this value elsewhere (as well)?  
  
// Perform the websocket handshake  
ws_.async_handshake(host_, "/foo",  
        beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-03-05 14:10:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1871#issuecomment-595248670  

When you inspect the handshake using wireshark, what is being sent?

---

## Comment 2

> Username: AnwaPeck  
> Created at: 2020-03-05 14:33:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1871#issuecomment-595259741  

Using SSL version... so having difficulty with inspection

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-03-05 14:37:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1871#issuecomment-595261700  

Use a decorator to get the request message and print it before it is sent:  
https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/using_websocket/decorator.html

---

## Comment 4

> Username: AnwaPeck  
> Created at: 2020-03-06 10:52:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1871#issuecomment-595714267  

My bad!  'Problem' solved.  Rookie cmake error on my side.  Build appeared to be successful but target executable was not being replaced.  Thanks for the prompt assist. Appreciated.  
  
B.T.W.  I found a decorator in the sample code I was using.. Then while following your instruction to print the message... I realised that I was executing a stale build.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-03-06 14:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1871#issuecomment-595802177  

Glad it got sorted!

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-04-05 15:42:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1871#issuecomment-609436670  

This issue has been open for a while with no activity, has it been resolved?
