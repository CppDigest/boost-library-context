# #1904 - static http message [Closed]

> Username: jpramosi  
> Created at: 2020-04-17 19:29:56 UTC  
> Updated at: 2020-04-18 19:17:50 UTC  
> Closed at: 2020-04-18 10:57:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1904  

My main goal is to read the http-to-websocket upgrade request from a client, analyze the header, create the websocket session and accept the attempt without any heap allocation during this procedure (heap allocations are only allowed at the start of the server).  
I started to make a http::message type with a custom allocator which could be later used in a custom parser.  
  
However i have encountered some problems:  
- local buffer in allocator (char buffer[]), wouldn't work.  
- maintaining a global list to keep track of the buffers and to access them from the custom allocator   
  is also not an option (i guess it would be too bulky and slow)  
- tls buffer allocator could work for some other cases, however for my situation not suitable  
  
After i have studied the documentation again. I had some doubts whether a static http message which resembles the flat_static_buffer in terms of the memory allocation style is possible at all or something similar can be achieved with or without the need of a http::message.  
  
Now i got some other things in my mind, but these things doesn't exist(?) in beast to the best of my knowledge, but would resolve my problem:  
  
- ideally something like   
  `message<std::size_t Fields, std::size_t FieldSize, boo isRequest, Body>` would fit perfect  
- practically i would need only the header-field information. It would be convenient to forward the   
  buffer to websocket::async_accept after i have read the message with http::async_read and do not   
  use http::message at all  
- websocket::async_accept could have an overload for a parser  
  
Maybe i have missed something obvious to resolve this problem.. but dunno how i should proceed.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-17 20:24:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615447686  

> Maybe i have missed something obvious to resolve this problem  
  
It was never intended to be able to perform a websocket handshake and subsequent websocket message exchange without ever performing a dynamic allocation.

---

## Comment 2

> Username: jpramosi  
> Created at: 2020-04-17 22:45:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615495800  

A more general question:  
What do you think about using an adopt allocator class in Beast which accepts a buffer in a combination with an instance to pass around instead of using std::allocator<> as template?  
  
It would work on a similar way using boost::asio::buffer to wrap a buffer and use it an operation. This  may allow a more flexible allocation strategy for users.  
However an adopt allocator with different styles like pool or linear allocation would be required ( incrementing a pointer to the right alignment for the next object should also do it).  
Now it seems like to be a small use case, but maybe it might be more useful for other things too in the future.  
if this bold idea isn't too odd, i can tinker an experimental proposal pull request if you would like.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-17 23:02:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615499788  

I have no idea what you're talking about. What is an "adopt allocator?"

---

## Comment 4

> Username: jpramosi  
> Created at: 2020-04-18 00:07:22 UTC  
> Updated at: 2020-04-18 00:09:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615517264  

For example you have have a buffer+size which the user has allocated. Now you could wrap this buffer like asio is doing it with boost::asio::buffer(buffer, size) and manage this buffer object yourself for your objects and operations.  
In the end you would replace std::allocator<> template argument with a buffer instance which can be passed to a class or function and manage this buffer with an "adopt_allocator".  
So adopt_allocator will use this buffer instance to construct the objects for further usage just like a common std::allocator<>.  
However as i have written above, this allocator its implemenation will be taken away from the user. But in exchange the user can pass now an instance instead of defining a type with an allocator.  
So these problems i mentioned above would be resolved.  
However nothing's perfect and i may overlook something? (especially if i'm tired like now) or maybe i need a totally different approach about these problems but i would be happy to talk about it tomorrow.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-04-18 00:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615517967  

I still don't understand what you're talking about. Are you proposing a change to Beast?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-04-18 00:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615518562  

Are you proposing a change to websocket? To Beast in general? Are you thinking to change the signature of a function? If yes, then can you write a C++ declaration representing the function signature you are proposing?

---

## Comment 7

> Username: jpramosi  
> Created at: 2020-04-18 00:27:12 UTC  
> Updated at: 2020-04-18 00:30:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615520802  

For now a more general experimental addition about the allocation concept i mentioned. Later if it does look good, i can imagine it to making changes specific to http respectively fields and the depending parser.  
Sorry for being too unclear about this.  
However dunno what exactly your plans are with the great http refactor and allocators, but maybe this idea with a different allocation approach could evolve to a different or even a good way to handle memory allocations generally.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-04-18 00:30:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615521367  

I don't know what the idea is, but C++ already has well-defined allocation models: _Allocator_ and `pmr::memory_resource`.

---

## Comment 9

> Username: jpramosi  
> Created at: 2020-04-18 00:34:27 UTC  
> Updated at: 2020-04-18 00:34:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615521957  

I can make a pull request about this. I believe code describes better my intentions :)

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-04-18 00:35:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615522085  

Well, if you think that's the best way, but I have my doubts. It might save you effort if you just provide a code snippet right here.

---

## Comment 11

> Username: jpramosi  
> Created at: 2020-04-18 10:57:58 UTC  
> Updated at: 2020-04-18 11:02:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615841421  

Okay, i have thought too much about this and made the problem more complex than it actually is. After i have read the conversation again, i have realized i can already do this on a similar way.  
I'm sorry for bothering you.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-04-18 19:17:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1904#issuecomment-615930350  

I'm always glad to help, as long as the problem is well defined :)
