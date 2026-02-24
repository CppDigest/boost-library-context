# #81 - Add protobuf example [Closed]

> Username: mzimbres  
> Created at: 2023-03-10 13:48:42 UTC  
> Updated at: 2023-03-11 12:10:40 UTC  
> Closed at: 2023-03-11 12:10:40 UTC  
> Url: https://github.com/boostorg/redis/issues/81  

Reference: https://protobuf.dev/

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-03-10 14:40:43 UTC  
> Updated at: 2023-03-10 14:43:18 UTC  
> Url: https://github.com/boostorg/redis/issues/81#issuecomment-1463901036  

Protocol Buffers are not that great... can this be a separate library? What is the motivating use-case for this? It sort of doesn't make sense that a low-level RESP3 client has to know about an ever-increasing amount of non-Redis things. Perhaps we could instead have a customization point allowing users to integrate whatever third party libraries they want?  
  
For example Cap'n Proto:  
https://capnproto.org/  
  
Or Boost.Serialization:  
https://www.boost.org/doc/libs/1_81_0/libs/serialization/doc/index.html  
  
There is a significant part of the C++ community that hates protocol buffers:  
https://reasonablypolymorphic.com/blog/protos-are-wrong/

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-03-10 15:04:01 UTC  
> Url: https://github.com/boostorg/redis/issues/81#issuecomment-1463933606  

What about having it only as an example?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-03-10 15:15:09 UTC  
> Url: https://github.com/boostorg/redis/issues/81#issuecomment-1463949677  

Examples are great, and preferred.
