# #1860 - Help handling callback to send data to websocket [Closed]

> Username: Xadiant  
> Created at: 2020-02-25 22:51:27 UTC  
> Updated at: 2020-03-02 23:16:06 UTC  
> Closed at: 2020-03-02 23:16:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1860  

I am starting a project that is currently very close to the advanced_server.cpp application and experimenting by expanding from there.  
  
What I am attempting to do is then a client connects a websocket I want to kick off a process that runs independently and occasionally spits out stdout data.  I want the server to send all of this output to the connected websocket until the pipe is closed. (And in the future also go the other way, pipe text from websocket to application.)  
  
So far I have the application being started and binding to a server function on process output.  This is working fine so far.  I am new to beast an am unsure if the memory I am working in is even valid, does the worker move on and I am just working in old memory at this point?  Also I am unsure out to write data into the websocket, if there are better examples please help point me there.  Do I need to create a new buffer to this... or can I re-use 'buffer_' in the example?  
  
Here is the section of code I am working in (assume the rest is advanced_server.cpp)  
`  
    void on_accept(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "accept");  
  
        if (target_ == "/echo") {  
        	do_echo_read();  
        }  
        else if (target_ == "/print") {  
  
        }  
        else if (target_ == "/shared_memory") {  
        	// start shared memory process  
        	childProcess_ = ChildProcess("../../SharedMemoryCounter/Release/SharedMemoryCounter",  
        			boost::bind(&websocket_session::on_sharedmem_read, this, _1)  
        	);  
        }  
    }  
  
    void on_sharedmem_read(char* data)  
    {  
    	std::cout << "got callback data: " << data << std::endl;  
    	std::cout << "target= " << target_ << std::endl;  
  
    	// send 'data' through ws_...  
    }  
`  
The argument to 'ChildProcess'  with the boost::bind callback is `boost::function<void(char*)>`  
  
I appreciate any help I can get.  Thanks!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-02-26 00:02:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1860#issuecomment-591151106  

It's late for me now but I can help with this tomorrow.   
The general form for sending data in beast/asio is to queue it into an output queue and initiate a write if there is not already a write in progress. Once a write has completed, remove the data from the queue and check whether more data has been queued. If so, initiate another write.  
In a server app you will generally be continually initiating and handling reads (in beast, this is actually what keeps the websocket running) and writing when there is data to write.

---

## Comment 2

> Username: Xadiant  
> Created at: 2020-02-26 18:31:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1860#issuecomment-591574087  

Thank you for the information!  
  
I figured out how to at least send the data along and that part is working well.  
  
`void on_accept(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "accept");  
  
        if (target_ == "/echo") {  
        	do_echo_read();  
        }  
        else if (target_ == "/print") {  
  
        }  
        else if (target_ == "/shared_memory") {  
        	// start shared memory process  
        	childProcess_ = ChildProcess("../../SharedMemoryCounter/Release/SharedMemoryCounter",  
        			boost::bind(&websocket_session::on_sharedmem_read, shared_from_this(), _1)  
        	);  
        }  
    }  
  
    void on_sharedmem_read(char* data)  
    {  
    	beast::flat_buffer b;  
  
    	beast::ostream(b) << data;  
  
    	ws_.write(b.data());  
    }  
`  
(not sure why I can't get the full code to display correctly here)  
  
I used shared_from_this() which I believe holds the websocket structure in memory so that it is still available at the time of the callback?  
  
How can I gracefully close the websocket session?  How do I get a callback/check whena websocket is disconnected (ie: refreshing from the client browser)?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-02-26 19:22:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1860#issuecomment-591598291  

> How do I get a callback/check whena websocket is disconnected (ie: refreshing from the client browser)?  
  
You have to make sure you always have a call to `async_read` pending. The call will error out, either with a timeout if you have configured it correctly, or with a socket closure error.

---

## Comment 4

> Username: Xadiant  
> Created at: 2020-03-02 23:16:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1860#issuecomment-593672771  

Just want to update so others can follow what I learned.  
  
Like Vinnie and madmongo said: I need to always have an async call pending.  That means after the connection is accepted I call async_read and the callback (after handling the received data) ends up calling async_read again.  
  
My other problem was that I also wanted to aync_write on the same websocket, but the writes were coming from another thread.  So I used net::post to essentially delegate the write to the correct thread.  I lifted how to do this (as well as using a queue to amke sure we receive all the messages) from the chat_server.cpp demo.  
  
Thank you for the help, I have been able to go a long ways after getting these initial building blocks in place.
