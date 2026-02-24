# #2047 - Wrong internal streamstate when reusing socket [Closed]

> Username: LeTobi  
> Created at: 2020-08-11 12:36:42 UTC  
> Updated at: 2020-08-11 16:16:42 UTC  
> Closed at: 2020-08-11 16:16:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2047  

I am working on a websocket library using beast. My intention is to reuse the necessary websocket::stream<> object for multiple connections.  
  
As it seems there is always an error with the internal streamstate at the 3rd connection of a stream. The docs do not state any reset functionality so I assume the streamstate should be reset automatically with a failed connection.  
  
I have created a small example with the scenario I stumbled upon: [LeTobi/beast-issue](https://github.com/LeTobi/beast-issue) (boost version 290)  
The error occurs when the connection gets closed by the peer at tcp level without shutdown using tcp::socket::close() after a successful handshake.  
  
I hope that the error is reproducible and it is no side-effect of some network-configuration.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-11 13:03:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2047#issuecomment-671933820  

I'm afraid you can't re-use a websocket stream object, it's internal state is not restartable.  
  
You will need to destroy it and recreate it. You can certainly steal the underlying socket/ssl state out of it first if necessary.

---

## Comment 2

> Username: LeTobi  
> Created at: 2020-08-11 13:41:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2047#issuecomment-671954332  

Oh, I didn't expect that.  
Because reusing the stream once seemed to work, I thought it was reset.  
  
Reconstructing the objects will probably work fine.  
Anyway, would there be a possibility to take ownership of the next layer? With "steal the underlying socket" do you refer to a intended method?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-11 15:42:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2047#issuecomment-672025678  

Use `std::move(ws.next_layer())` to construct a new stream

---

## Comment 4

> Username: LeTobi  
> Created at: 2020-08-11 16:16:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2047#issuecomment-672062260  

This will work.  
Thank you for the tip!
