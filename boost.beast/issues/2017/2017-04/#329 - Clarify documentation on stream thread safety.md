# #329 - Clarify documentation on stream thread safety [Closed]

> Username: vinniefalco  
> Created at: 2017-04-27 20:19:30 UTC  
> Updated at: 2017-05-08 00:04:00 UTC  
> Closed at: 2017-05-08 00:04:00 UTC  
> Url: https://github.com/boostorg/beast/issues/329  

`websocket::stream` docs should state:  
  
**Thread Safety**  
_Distinct objects_: Safe.  
_Shared objects_: Unsafe.  
  
This applies to both the synchronous and asynchronous interfaces.
