# #3017 - Change request parser body type after receiving part of message body [Closed]

> Username: korydraughn  
> Created at: 2025-07-09 01:48:51 UTC  
> Updated at: 2025-07-15 19:11:13 UTC  
> Closed at: 2025-07-15 19:11:13 UTC  
> Url: https://github.com/boostorg/beast/issues/3017  

I'm using Boost 1.81.  
  
Is it possible to change the body type associated with the request parser after receiving part of the message from the client?  
  
I know there's an example in the docs which demonstrates changing the body type based on headers.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-07-11 05:09:09 UTC  
> Url: https://github.com/boostorg/beast/issues/3017#issuecomment-3060560179  

No, this isn't possible. The closest alternative is to use a non-owning body type such as [http::buffer\_body](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body.html) or [http::span\_body](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__span_body.html), and then manage how the result is stored yourself.

---

## Comment 2

> Username: korydraughn  
> Created at: 2025-07-15 19:11:13 UTC  
> Url: https://github.com/boostorg/beast/issues/3017#issuecomment-3075148292  

Got it. Thanks.
