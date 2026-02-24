# #189 - Is syslog considered to support tcp socket [Closed]

> Username: Jackson-soft  
> Created at: 2022-07-09 10:34:34 UTC  
> Updated at: 2022-07-09 15:30:35 UTC  
> Closed at: 2022-07-09 15:30:35 UTC  
> Url: https://github.com/boostorg/log/issues/189  



---

## Comment 1

> Username: Lastique  
> Created at: 2022-07-09 15:30:35 UTC  
> Url: https://github.com/boostorg/log/issues/189#issuecomment-1179562358  

The current implementation is consistent with [RFC3164](https://datatracker.ietf.org/doc/html/rfc3164), which only defines UDP as the transport protocol. [RFC5424](https://datatracker.ietf.org/doc/html/rfc5424) also defines UDP as the primary transport, though there is [RFC6587](https://datatracker.ietf.org/doc/html/rfc6587) that additionally defines TCP.  
  
Updating implementation to the newer specifications would be desirable and should be done at some point, but there are no immediate plans to do this.
