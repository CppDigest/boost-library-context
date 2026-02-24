# #2767 - Dual WebSocket channels on a C++ server [Closed]

> Username: nekkkoS  
> Created at: 2023-11-14 16:44:41 UTC  
> Updated at: 2023-12-04 09:54:26 UTC  
> Closed at: 2023-12-04 09:54:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2767  

I've explored the example server available at this link #2766 https://github.com/vinniefalco/CppCon2018, and I'm a little unsure of the best approach to implementing a particular functionality. I need to set up two WebSocket channels on the same server, both serving different purposes.  
  
The first WebSocket connection is supposed to consistently send data to the client at a high frequency (The server receives data about hardware performance and sends it to the client). Meanwhile, the second connection is intended to receive alerts and commands to control the server.  
  
Could you please give recommendations or point me in the right direction on how to achieve this?

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-14 18:04:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2767#issuecomment-1810846085  

In the [handshaking process](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/using_websocket/handshaking.html), you have the flexibility to use different `request-target` values. In the client-side code, you can pass a different target parameter to [async_handshake](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_handshake/overload1.html), while in the server-side code, you can utilize the specified overload of [async_accept](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html) to inspect the `request-target` field in the request and decide what to do with the connection.  
  
Alternatively, you can initiate communication with the server by sending a message at the beginning of each connection. Inside the server, after reading the initial message, you can determine the appropriate actions to take based on the content. For instance, if you are using JSON messages, a message could look like this:  
  
```JSON  
{  
    "role": "monitoring"  
}  
```
