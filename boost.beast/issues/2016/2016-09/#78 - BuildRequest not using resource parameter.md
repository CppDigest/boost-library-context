# #78 - BuildRequest not using resource parameter [Closed]

> Username: PortableNuke  
> Created at: 2016-09-09 16:13:39 UTC  
> Updated at: 2016-09-09 20:25:42 UTC  
> Closed at: 2016-09-09 20:25:42 UTC  
> Url: https://github.com/boostorg/beast/issues/78  

I'm just getting started with websocket programming and thought that the Beast library would be a good place to started. I've been trying to put together a simple app that communicates with a websockets server and I noticed that the handshake is always issued as GET / rather than, for example, GET /myserver/serverpoint.  
  
I notice that although you are expected to provide a resource during the handshake call this parameter, which is passed into the build_request function, is ignored and the url is explicitly set to "/". This looks like an oversight - shouldn't the resource be used in the construction of the req.url?  
  
PortableNuke.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-09 16:21:25 UTC  
> Updated at: 2016-09-09 16:21:45 UTC  
> Url: https://github.com/boostorg/beast/issues/78#issuecomment-245963262  

@PortableNuke You are correct, and this issue has been fixed in **1.0.0-b11** as of August 26th:  
See https://github.com/vinniefalco/Beast/commit/878c0f2a19887114ca30c2ae4d298b8008985d5b  
  
Current version is **1.0.0-b13** you should upgrade :-)  
  
Thanks for the heads up!

---

## Comment 2

> Username: PortableNuke  
> Created at: 2016-09-09 20:25:42 UTC  
> Url: https://github.com/boostorg/beast/issues/78#issuecomment-246029656  

Okay, that's good to know. I'll make sure to upgrade to the latest version.  
  
Thanks,  
  
PortableNuke
