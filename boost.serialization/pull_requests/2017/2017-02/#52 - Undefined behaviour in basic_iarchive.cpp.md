# #52 Undefined behaviour in basic_iarchive.cpp [Closed]

> Username: olologin  
> Created at: 2017-02-22 20:15:23 UTC  
> Updated at: 2017-05-28 19:33:27 UTC  
> Closed at: 2017-05-28 19:33:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/52  

I found this UB while using boost serialization at work, and of course, I have to say, it was caused by incorrect usage of boost::serialize from my side, because I tried to deserialize object of type, that wasn't actually in container. Anyway, I don't think this should be the cause for undefined behaviour. It should throw at least some exception in release build. The problem was detected accidentally in debug build, "subscript out of range" exception was thrown by vector [] operator.  
  
Sorry that I don't have reproducible example of serialization/deserialization that causes the problem. I'm too lazy to simplify our closed code. I hope people that are good at this great library will understand cause of this problem without examples.

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-05-23 19:50:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/52#issuecomment-303512212  

this looks like a side effect from some other error.  How did this happen in the first place?

---
