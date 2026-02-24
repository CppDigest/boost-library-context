# #237 - Make opcode private [Closed]

> Username: vinniefalco  
> Created at: 2017-01-24 21:38:31 UTC  
> Updated at: 2017-06-21 01:05:15 UTC  
> Closed at: 2017-06-21 01:05:15 UTC  
> Url: https://github.com/boostorg/beast/issues/237  

`websocket::opcode` should be a private type since users can only get `text` and `binary` values out of it, this will reduce confusion. We can make a new type `message_type` enum that has just two values.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 01:05:15 UTC  
> Url: https://github.com/boostorg/beast/issues/237#issuecomment-309933392  

Done
