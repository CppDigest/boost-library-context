# #1705 - Backward compatibility [Closed]

> Username: dk1013  
> Created at: 2019-09-16 01:01:47 UTC  
> Updated at: 2019-09-16 01:08:40 UTC  
> Closed at: 2019-09-16 01:08:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1705  

I have a rather general question regarding backward compatibility (Not sure if this is the best place to raise it though).  
So if I have some code using an older version of beast, is it guaranteed that the old code will run correctly with newer versions?  
If not, will there be any performance improvement if I adapt my code to the newer version?  
BTW, thanks for the amazing library!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-16 01:05:31 UTC  
> Updated at: 2019-09-16 01:05:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1705#issuecomment-531616278  

In terms of protocol compatibility, any version of Beast can talk to any other version of Beast using the HTTP or WebSocket protocols. If you're asking about API compatibility, I try not to break things but sometimes it is not avoidable. For example, Asio made changes which do require a code update. In general, newer versions of Beast are better than older versions, in one or more ways:  
- Faster compilation  
- Smaller code generation  
- Better performance  
- More functionality  
- Increased security  
- Bug fixes  
  
It is in the best interests of programs which use Beast, to use the latest version of the library, to take advantage of bug fixes and security improvements.

---

## Comment 2

> Username: dk1013  
> Created at: 2019-09-16 01:08:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1705#issuecomment-531616507  

Thanks @vinniefalco , the response is really fast.  
Well noted, will try to migrate to latest version.
