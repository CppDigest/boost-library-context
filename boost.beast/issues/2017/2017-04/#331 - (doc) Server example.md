# #331 - (doc) Server example [Closed]

> Username: surgura  
> Created at: 2017-04-30 08:24:23 UTC  
> Updated at: 2017-06-21 01:21:46 UTC  
> Closed at: 2017-06-21 01:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/331  

In the doc and on github there is a clear example of how to connect as a client. Adding a server example could be useful.  
  
Reasoning: The first time I tried beast I got scared off and went with a more works out of the box solution.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-30 23:56:17 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-298265591  

You mean like synchronous and asynchronous HTTP and WebSocket servers? These?  
https://github.com/vinniefalco/Beast/blob/master/examples/http_sync_server.hpp  
https://github.com/vinniefalco/Beast/blob/master/examples/http_async_server.hpp  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_async_echo_server.hpp  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_sync_echo_server.hpp  
  
These examples have been in the project for over a year! Were you not able to find them?

---

## Comment 2

> Username: surgura  
> Created at: 2017-05-01 06:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-298295429  

These are really good examples. The difference with the example in the readme(frontpage of the repository @ usage) is that connecting as a client is a very minimal example and these don't feel like that. I do not want to nag, but the first thing I expected to see when reading the readme is such an example. It might seem like I'm stupid, but I just want to give feedback using my experience with the library.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-01 06:57:48 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-298295997  

Beast doesn't really aim to offer an "out of the box" server solution, it only aims to provide the low level building blocks of HTTP and WebSocket. There are so many ways to implement a server each with its own tradeoffs, that I couldn't possibly write it a single particular way that would satisfy everyone in a general purpose library. The examples intentionally show a synchronous client, since that results in the shortest code!

---

## Comment 4

> Username: surgura  
> Created at: 2017-05-01 08:36:49 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-298305473  

By out of the box I meant 'clear where to start'. I do not expect beast to provide a complete framework. However, I feel like the examples you provided look a bit like such a framework, while in the basics accepting a client is very simple with beast.  
- accept client with boost::asio  
- do handshake with beast  
- send/receive with beast  
This gives users a clear view of what to expect. And if they want to do more they can find it in the documentation. I think the examples try to explain too much at once.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-05-01 14:35:27 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-298344203  

Oh! So maybe, you're saying - I could add a simple HTTP server and WebSocket server example which is synchronous and handles only one connection? That would fit into a slightly larger `main` than the current client examples.

---

## Comment 6

> Username: surgura  
> Created at: 2017-05-01 14:47:59 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-298346618  

Yes that is what I mean. I think that would help a lot of new users.

---

## Comment 7

> Username: surgura  
> Created at: 2017-05-01 14:51:12 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-298347287  

I would create an example myself, but to be honest I currently am myself one of those users who would benefit :)

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-06-08 20:07:47 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-307212932  

@surgura Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-21 01:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-309935589  

**example/server-framework** is a comprehensive, working example of a server.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-21 01:21:46 UTC  
> Url: https://github.com/boostorg/beast/issues/331#issuecomment-309935673  

>I do not expect beast to provide a complete framework.   
  
Beast provides a complete framework now :)
