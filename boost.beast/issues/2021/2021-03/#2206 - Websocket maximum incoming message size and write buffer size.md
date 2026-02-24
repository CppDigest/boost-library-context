# #2206 - Websocket maximum incoming message size and write buffer size [Closed]

> Username: philippeVerney  
> Created at: 2021-03-25 10:13:05 UTC  
> Updated at: 2021-03-25 12:45:45 UTC  
> Closed at: 2021-03-25 12:45:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2206  

Hi,  
  
Looking at [websocket::stream::read_message_max](https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/read_message_max/overload1.html), I am wondering what is exactly the message size?  
Is it:  
- The whole websocket message size including all frames FIN, RSV, opcode, mask, payload length etc.. bytes?  
- The sum of all data frames payload data size?  
- The sum of all data frames application data size?  
- something else?  
  
I have also a similar question about [websocket::stream::write_buffer_bytes](https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/write_buffer_bytes/overload1.html) .  
Is this size constraining each written :  
- whole websocket frame size?  
- websocket frame payload data size?  
- websocket frame application data size?  
- something else?  
  
Thanks for this great library

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-25 12:04:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2206#issuecomment-806610842  

The values represent the uncompressed size of the frame payload. i.e. they do not include the framing protocol (FIN, RSV, opcode, mask, payload length etc.. bytes)

---

## Comment 2

> Username: philippeVerney  
> Created at: 2021-03-25 12:45:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2206#issuecomment-806675149  

Thank you!
