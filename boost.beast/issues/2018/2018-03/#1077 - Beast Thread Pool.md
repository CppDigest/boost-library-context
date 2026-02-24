# #1077 - Beast Thread Pool [Closed]

> Username: avalchev  
> Created at: 2018-03-24 13:50:56 UTC  
> Updated at: 2018-03-24 17:20:30 UTC  
> Closed at: 2018-03-24 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1077  

I have some conceptual problems with Beast (and probably Asio).  
  
I suppose I need to implement thread pool where requests are placed and processed concurrently and free Beast/Asio `io_context` threads for read/write operations.  
  
Could you please point me the best solution, how to implement this thread pool for processing long running request (db queries, html rendering and all other high level stuff). What "technology" should I use:  
  
- Thread pool, like boost::asio::thread_pool  
- Try to get more into asio details and figure out how to build such pool with / by extending executors or io_service.

---

## Comment 1

> Username: cmazakas  
> Created at: 2018-03-24 16:25:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1077#issuecomment-375905396  

In Asio, threads register themselves for work by calling `run` on an `io_context`. So if you want multiple threads, have each thread `io.run();`. A few of the Beast server examples demonstrate this.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-03-24 17:19:23 UTC  
> Updated at: 2018-03-24 17:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1077#issuecomment-375909563  

Long-running, synchronous program operations should not be executed an `io_context` associated with active connections, otherwise they will block incoming network data from being processed and outgoing network data from being sent. Instead, delegate this work to your own thread pool. `boost::asio::thread_pool` is one solution, or you can also write your own if you want to have things like work priorities or more control over how work is ordered. You should not need to mess with executors. You can also use a separate instance of `io_context` as a make-shift thread pool. That decision will depend on the specifics of your use-case.  
  
When the long-running operation is completed, use `boost::asio::post` to queue a call to a function which calls an asynchronous initiating function to deliver the result to the connection. Make sure to use the `io_context` associated with the connection and also a strand (implicit, or explicit).

---

## Comment 3

> Username: avalchev  
> Created at: 2018-03-24 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1077#issuecomment-375909642  

Thanks :)
