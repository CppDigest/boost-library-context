# #1257 - Problem in proper clean up after request processed from the advance server due to timer. [Closed]

> Username: gauravrai0316  
> Created at: 2018-09-26 10:08:36 UTC  
> Updated at: 2018-11-28 02:10:00 UTC  
> Closed at: 2018-11-28 02:10:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1257  

### Version of Beast:  167  
  
I have created a server with the help of advance_server example (but only using http protocol), which runs on a single thread. The server accepts GET and POST requests. Server returns a basic HTML page for the GET request. For post requests (request in binary format sent to server using curl), it returns a binary file consisting the output from server.   
  
   According to my understanding, listener class will keep on accepting a new request and send it to httpsession class to read, process and write back to stream output. For every request, a new socket is created and closed when the request is processed (after the timer expired).   
  
Since the server reading and writing asynchronously, I am getting the server response properly in binary format. But the destructor of the httpsession class is not called until the timer is expired and that is bothering me.  
  
do_close() is used to close the session gracefully, so I added the following line in the function to cancel any asynchronous wait immediately, when my request is processed:  
*******************************************************************************  
// only change in advance_server.cpp in the following function  
      void do_close()  
      {  
         /* Send a TCP shutdown */  
         socket_.shutdown(tcp::socket::shutdown_send);  
         timer_.cancel();      // added to close the timer immediately  
         /* At this point the connection is closed gracefully */  
      }  
*******************************************************************************  
  
This will release the timer and cleaning up (destructor calls) will be done properly.  
  
Is this the right approach or does it have side-effects?  
  
I have following two scenarios for cleanup:  
1. If I use the keep_alive flag of request (which is true), do_close() function is called when end_of_stream is reached, which takes a lot of time to occur. No idea, why?  
2. If I set the keep_alive flag  false in response, then need_of() will close the async_write and do_close() will be called.  
  
Can you please suggest how to do it properly?  
  
Sorry for my negligence, as it is my first project on network programming.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-07 20:00:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1257#issuecomment-427682218  

There was a bug in the timer handling for the advanced servers, the fix has been pushed to the develop and master branches - please let me know if this problem persists. Thanks!

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1257#issuecomment-441859456  

This issue has been open for a while with no activity, has it been resolved?
