# #1261 - HTTP Bandwidth Throttling [Closed]

> Username: carolinebeltran  
> Created at: 2018-10-04 03:47:39 UTC  
> Updated at: 2018-10-29 18:22:45 UTC  
> Closed at: 2018-10-29 18:22:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1261  

I am hoping to get some advise on the approach or approaches that I should consider to throttle (limit) the rate that a Beast **HTTP** server streams data to a client.  
  
Let's assume that a user wants to watch a 300MB video hosted by the Beast HTTP server.  In most cases, the client's internet bandwidth is greater than the rate consumed by the video player.  This means that the 300MB video could buffer locally in just a few minutes.  In this short time-span, the user may have watched only 10MBs worth of video, then decides to view a different one instead.  In this case, we have 290MB of wasted video transferred to the browser.  It would have been much better to have only served 10MB + a little padding.  
  
If we make an educated guess to what the maximum transfer rate should be, we can save on finite bandwidth.  I've worked with video before and video data consumption is variable, so I won't deal with that, but I can calculate a padded **constant maximum transfer rate**.  
  
Having calculated the max rate, I'd like to get some general perspectives on how to apply HTTP throttling on a Beast Async HTTP server.  
  
Please advise, thank you.  
  
P.S.  Before I begin writing code to accomplish this with Beast, I'd like to hear from some experts first.  
  
My only idea is that I can set a max throttle rate by 1) keeping track of the **current time and bytes transferred** for each connection, and then 2) **sleeping the current thread n milliseconds**.    
  
I think that this approach would work if **io_context runs a pool of threads** and the server does not have many user connections.  But if this is a busy server with many users, I believe that this approach would suffer terribly because **all available threads would block due to the sleep** and any Async benefits would be void.  This is why I am looking for other perspectives.  Thanks again.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-04 05:07:33 UTC  
> Updated at: 2018-10-04 05:11:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1261#issuecomment-426886916  

Well, this is a great question. And there are general Asio solutions to this problem which would also work for Beast. Sleeping is of course not a good idea, for the reason you described. Instead, you should use a timer with a fairly coarse interval (say 3 seconds to start with), and limit the amount of bytes that you transact in each interval. You can take the desired maximum bandwidth and divide in order to calculate the limit of bytes.  
  
When a write completes, you check how much time has elapsed and then if not enough time has passed, wait for the next interval to start before sending more data, otherwise send right away. The TCP/IP protocol will automatically adjust the flow control window based on how your application behaves. Each time a send completes, you can recalculate the actual bandwidth and compare that with the desired bandwidth to determine how many bytes to write next.  
  
To restrict the number of bytes transacted during serialization, use the serializer-oriented interface and call this function to enforce an upper limit on the number of bytes when performing asynchronous writes using `http::async_write_some`.  
  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__serializer/limit/overload2.html  
  
Another possibility is to build the bandwidth-throttling feature directly into a stream wrapper which meets the requirements of the **AsyncWriteStream** concept, such a component would operate transparently and be general purpose (i.e. it would work not only with Beast but any asynchronous algorithm template which used the concept).  
  
I have been exploring the technique of building a timeout feature into a stream wrapper to make calls to `async_read_some` have a built-in timeout. This code is not ready for production and I do not have any time table on when it might be, but you can have a look at it to get ideas:  
  
https://github.com/vinniefalco/beast/blob/timed_stream/include/boost/beast/experimental/core/timed_socket.hpp#L25

---

## Comment 2

> Username: carolinebeltran  
> Created at: 2018-10-29 18:22:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1261#issuecomment-434024561  

Thank you for your help Vinnie.  At this time, I am able to very efficiently stream binary data via HTTP using Beast.  I personally resorted to Async methods and timers (no sleep thank goodness) to effectively do this.  This means that a single threaded Beast app can stream data to many clients very efficiently (extremely low RAM and CPU).
