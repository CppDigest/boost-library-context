# #1840 - How to use asio:streambuf in websocket.read [Closed]

> Username: killerall  
> Created at: 2020-02-07 02:22:27 UTC  
> Updated at: 2020-02-10 12:55:44 UTC  
> Closed at: 2020-02-10 12:55:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1840  

Hi,  
I'm adding support for websocket in an application that uses asio :: socket, synchronously.  
I adjusted almost everything, however, I have a problem with reading.  
In the current application I have:  
  
`boost::asio:streambuf readBuffer;  
`  
The above buffer is used later, and I can`t change it.  
  
To perform the reading, I currently do:  
  
```  
  
       #define BUFFER_SIZE 1024  
	boost::system::error_code error;                        
	boost::asio::streambuf::mutable_buffers_type bufs = readBuffer.prepare(BUFFER_SIZE);     
	size_t socketTotal;  
	socketTotal = socket->read_some(bufs, error);  
	if (error)  
		throw boost::system::system_error(error);  
	readBuffer.commit(socketTotal);  
```  
  
Following the examples of Beast (https://github.com/boostorg/beast/blob/develop/example/websocket/client/sync/websocket_client_sync.cpp)  
I read it like this:  
```  
  
	// This buffer will hold the incoming message  
        beast::flat_buffer buffer;  
  
        // Read a message into our buffer  
        ws.read(buffer);  
  
  
```  
So, ws.read does not support mutable_buffer_type, or any other streambuf, is it possible to do some kind of cast?  
  
I'm on boost 1.71  
### Version of Beast  
BOOST_BEAST_VERSION 266  
  
Thanks a lot!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-02-07 02:48:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1840#issuecomment-583209462  

Beast is not compatible with the Asio dynamic buffers, especially the older ones like `streambuf`. If you want this to work, you need to call `streambuf::prepare` and pass that to `beast::websocket::stream::read_some` (or `beast::websocket::stream::async_read_some`). After the read completes, call `streambuf::commit` with the value returned by read to finalize the buffer. Keep calling `read_some` until `websocket::stream::is_message_done()` returns `true`.  At this point your streambuf will have the message.

---

## Comment 2

> Username: killerall  
> Created at: 2020-02-07 20:05:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1840#issuecomment-583589201  

Thanks for the help, the reading is working.  
  
Now, when trying to close the connection, with websocket and SSL:  
  
`web_socket_ssl-> close (websocket :: close_code :: normal);`  
  
  an exception is triggered, the stream is truncated.  
  
If I use without SSL, the problem does not occur.  
  
Any idea what I'm doing wrong?  
  
Thanks again.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-02-07 21:19:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1840#issuecomment-583617537  

You probably aren't doing anything wrong. The error is to inform you that the SSL connection was not closed gracefully. Google servers do this all the time, as do others.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-02-10 11:59:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1840#issuecomment-584089260  

Hi @killerall ,  
  
Asio returns the result of io operations in an error_code. For the synchronous functions that don't take an error_code& as a parameter, a non-empty error code is presented as an exception.  
  
The problem is that there are a few conditions reported by ASIO that, although they are returned as an error_code, are not actually errors per-se.  
  
An example is asio::error::eof which indicates either that the remote peer issued a `shutdown` against the socket or that the file descriptor underlying a stream has entered the end-of-file state.  
  
If you're going to use the exception-producing form of synchronous functions, these non-errors will need to be caught and handled differently to the rest.  
  
From memory, errors that require special handling are:  
  
```  
asio::error::eof                   - peer issued shutdown()  
  
asio::ssl::error::stream_truncated - on an ssl stream, most likely due to  
                                     peer issuing shutdown()  
  
asio::error::connection_aborted    - during `accept()` the peer did not   
                                     complete the tcp handshake. This is  
                                     informational, not an error  
```

---

## Comment 5

> Username: killerall  
> Created at: 2020-02-10 12:55:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1840#issuecomment-584108952  

Hi   
I did the protection of stream_truncated, I will do the indicated by @madmongo1 also.  
  
Thank a lot.
