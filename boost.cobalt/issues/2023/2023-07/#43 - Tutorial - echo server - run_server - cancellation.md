# #43 - Tutorial > echo server > run_server > cancellation [Closed]

> Username: anarthal  
> Created at: 2023-07-24 08:51:18 UTC  
> Updated at: 2023-07-24 09:36:42 UTC  
> Closed at: 2023-07-24 09:36:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/43  

> Constructor the listener (destruction will cause cancellation!)  
  
Seems to be syntax-incorrect. I'd suggest to reword it, too:  
  
> Construct the listener generator coroutine. When the object is destroyed, the coroutine will be cancelled, performing all required cleanup.
