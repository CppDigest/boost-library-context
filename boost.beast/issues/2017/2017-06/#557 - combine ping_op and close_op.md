# #557 - combine ping_op and close_op [Closed]

> Username: vinniefalco  
> Created at: 2017-06-29 17:47:40 UTC  
> Updated at: 2017-08-16 00:28:31 UTC  
> Closed at: 2017-08-16 00:28:31 UTC  
> Url: https://github.com/boostorg/beast/issues/557  

These two composed operations have the same state and almost the same code, they can be combined into `control_op` to handle all three cases (ping, pong, control) with a little bit of CRTP or using a Policy template type that handles the minor differences (for example `close_op` sets `wr_close_=true`)  
  
All ping, pong, close members of stream can just reside in control.ipp and do away with ping.ipp and close.ipp

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-16 00:28:31 UTC  
> Url: https://github.com/boostorg/beast/issues/557#issuecomment-322625164  

`close_op` is now sufficiently different that this issue is no longer applicable.
