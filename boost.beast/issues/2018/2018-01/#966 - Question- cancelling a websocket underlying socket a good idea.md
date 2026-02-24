# #966 - Question: cancelling a websocket underlying socket a good idea? [Closed]

> Username: hoditohod  
> Created at: 2018-01-04 15:12:59 UTC  
> Updated at: 2018-01-08 18:34:06 UTC  
> Closed at: 2018-01-08 18:34:06 UTC  
> Url: https://github.com/boostorg/beast/issues/966  

Hi Vinnie,   
This is a just a question not an issue. I have a websocket server where async reads and writes on the socket are concurrently in progress (at most one of each type as the documentation states). When I receive an error in the read handler for example, for some error cases I want to abruptly terminate the websocket session. Is it a good idea to kill the other handler with socket.next_layer().cancel(). Or is there any better way to cancel an async op on a websocket stream which I want to terminate as quickly as possible?  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-04 15:48:24 UTC  
> Updated at: 2018-01-04 15:49:00 UTC  
> Url: https://github.com/boostorg/beast/issues/966#issuecomment-355316896  

`cancel` is what you want. This brings up an interesting point, the example websocket servers should probably call `cancel` on error. Although I don't think they are full-duplex.

---

## Comment 2

> Username: hoditohod  
> Created at: 2018-01-04 19:50:37 UTC  
> Url: https://github.com/boostorg/beast/issues/966#issuecomment-355381109  

Dumb question: you mean the next_layer().cancel() on the websocket::stream, right? Because I don't see cancel() on the websocket::stream itself.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-04 20:15:25 UTC  
> Url: https://github.com/boostorg/beast/issues/966#issuecomment-355387137  

Correct

---

## Comment 4

> Username: hoditohod  
> Created at: 2018-01-08 18:34:03 UTC  
> Url: https://github.com/boostorg/beast/issues/966#issuecomment-356053541  

Thank you!  
  
Beast is awesome, keep up the good work! :)
