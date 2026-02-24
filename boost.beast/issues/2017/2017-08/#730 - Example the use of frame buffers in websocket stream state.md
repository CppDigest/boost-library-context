# #730 - Example the use of frame buffers in websocket stream state [Closed]

> Username: vinniefalco  
> Created at: 2017-08-11 03:41:46 UTC  
> Updated at: 2017-08-16 00:00:05 UTC  
> Closed at: 2017-08-16 00:00:05 UTC  
> Url: https://github.com/boostorg/beast/issues/730  

It seems we need both `rd_.fb` and `wr_.fb` for the case where `ping_op` and `read_some_op` are both active, all code which uses the control frame buffer needs an audit.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-16 00:00:05 UTC  
> Url: https://github.com/boostorg/beast/issues/730#issuecomment-322621308  

Done
