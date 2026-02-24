# #2362 - Asynchronous Access of Database for Boost Beast [Closed]

> Username: WarrenN1  
> Created at: 2021-12-16 04:40:24 UTC  
> Updated at: 2022-06-16 14:28:08 UTC  
> Closed at: 2022-06-16 14:28:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2362  

I am trying to build a C++ REST API using boost beast on top of a database like MySQL or Postgres, but most of the libraries to access these databases utilize blocking. Is there a good boost programming pattern or library that works with boost::beast and boost::asio that would allow me to take advantage of the asynchronous framework provided by beast for querying my DB?  
  
I am also open to learning new database paradigms if necessary if mysql and postgres cannot be easily integrated with beast.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-16 06:47:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-995489522  

You need to dispatch long-running or blocking work to separate threads that are not calling `io_context::run`.

---

## Comment 2

> Username: malixi94  
> Created at: 2021-12-16 07:48:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-995522007  

You can take a look at issue #2132 which contains a proposed solution to this problem with sample codes. That issue seems to be the only thorough treatment of this subject in the Beast GitHub space. (Vinnie also comments on the subject in #1077)  
  
@vinniefalco might you know of any other treatment of this subject matter?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-12-16 08:03:14 UTC  
> Updated at: 2021-12-16 08:03:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-995531062  

There is an asio-based async mysql library if you want a fully async solution:  
https://github.com/anarthal/mysql  
This has not yet been updated to take advantage of cancellation signals, but that may not matter.  
  
If you want to use a synchronous library, you can dispatch work to a `asio::thread_pool`. If you want to asynchronously wait until the work has completed, you would pass in an _async_semaphore_ and wait on it. The long running task (such as the database query) would then release the semaphore when complete. In much the same way as you would use a promise/future on a thread-based architecture.  
  
If you are using coroutines, it becomes very easy as co_spawn will do the hard work for you:  
  
```  
given:  
asio::thread_pool workers;  
  
then:  
  
asio::awaitable<result_set>  
do_query(std::string query_string)  
{  
  // sync code here which will block a thread on the thread_pool executor  
  auto con = mydeb::connect(...);  
  auto results = con.query(query_string);  
  co_return results;  
}  
  
asio::awaitable<void> my_coro()  
{  
  auto results = co_await  
      asio::co_spawn(  
          workers.get_executor(),   
          do_query("select * from foo_table;"),   
          asio::use_awaitable);  
  // use the results here  
}  
```  
  
For a more versatile solution that works with C++ < 20, I'll add an example in my https://github.com/madmongo1/asio_experiments repo.  
  
Give me an hour.

---

## Comment 4

> Username: ddevienne  
> Created at: 2021-12-16 08:41:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-995557387  

@madmongo1 Thanks. I'm in a similar situation, needing to call into synchronuous PostgreSQL libpq from a WebSocket server. I don't have access to an async client (@anarthal also has a PostgreSQL async client, https://github.com/anarthal/postgres-asio, but it is not production ready like his MySQL one), nor do I have access to C++20, only C++17 (I'm already _lucky_ to have that even...).  
  
One thing I don't quite understand though, is why we need separate executors / thread-pools between ASIO and the WORK executing _tasks_. The [example ](https://github.com/boostorg/beast/issues/2132#issuecomment-737427440) from #2132 does that, but appears _complicated_ to me, _composing_ and _juggling_ executors. And Vinnie just said as much above again.  
  
In my case, I **am** _getting OFF the IO thread_ by posting to a chain of `strand`s, but those are scheduled on the `io_context` directly. In my server, each WS connection has a fixed number of strands, with the last strand in the chain sending/posting the response back to ASIO. This _request pipeline_ allows concurrent decode-request-from-bytes, process-struct-request, encode-struct-response-to-bytes. I also track _in-flight_ requests to limit their number per-connection. What is wrong with this approach? I don't really get why having this machinery on the `io_context` is somehow _bad_. I've shared some of that on the Boost ML too, but this thread seems to be on-point, so I thought I'd ask again here and now. It seems to me that **blocking** the IO thread is bad, but since I don't block and instead post/schedule the actual blocking processing to later, to be dispatched by the `io_context`, isn't that good enough?

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-12-16 08:57:51 UTC  
> Updated at: 2021-12-16 08:59:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-995568821  

Here's a working example that uses my `asioex::async_semaphore` type (which you are welcome to copy).  
This code is written in terms of standalone asio, but would be trivial to adapt for boost::asio  
  
https://github.com/madmongo1/asio_experiments/blob/2806a2db3617d8a39456508598ff9b0965cf0cce/long_running.cpp  
  
Example output:  
  
```  
program starting  
task 1 starting  
task 1 waiting  
task 2 starting  
task 2 waiting  
task 3 starting  
task 3 waiting  
long running task 2 starting  
long running task 2 tick 0  
long running task 1 starting  
long running task 1 tick 0  
long running task 3 starting  
long running task 3 tick 0  
long running task 2 tick 1  
long running task 1 tick 1  
long running task 3 tick 1  
long running task 2 tick 2  
long running task 1 tick 2  
long running task 3 tick 2  
long running task 2 tick 3  
long running task 2 complete  
task 2 complete  
long running task 3 tick 3  
long running task 3 complete  
long running task 1 tick 3  
long running task 1 complete  
task 3 complete  
task 1 complete  
program stopped  
```  
  
Note that you can simulate the use of the semaphore with an `asio::steady_timer` with a timeout set to `asio::stready_timer::time_point::max()`. Simply cancel the timer to "release" the semaphore. The timer's async_wait will complete with error `asio::error::operation_aborted`.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-12-16 09:05:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-995574736  

> @madmongo1 Thanks. I'm in a similar situation, needing to call into synchronuous PostgreSQL libpq from a WebSocket server. I don't have access to an async client (@anarthal also has a PostgreSQL async client, https://github.com/anarthal/postgres-asio, but it is not production ready like his MySQL one), nor do I have access to C++20, only C++17 (I'm already _lucky_ to have that even...).  
>   
> One thing I don't quite understand though, is why we need separate executors / thread-pools between ASIO and the WORK executing _tasks_. The [example ](https://github.com/boostorg/beast/issues/2132#issuecomment-737427440) from #2132 does that, but appears _complicated_ to me, _composing_ and _juggling_ executors. And Vinnie just said as much above again.  
>   
> In my case, I **am** _getting OFF the IO thread_ by posting to a chain of `strand`s, but those are scheduled on the `io_context` directly. In my server, each WS connection has a fixed number of strands, with the last strand in the chain sending/posting the response back to ASIO. This _request pipeline_ allows concurrent decode-request-from-bytes, process-struct-request, encode-struct-response-to-bytes. I also track _in-flight_ requests to limit their number per-connection. What is wrong with this approach? I don't really get why having this machinery on the `io_context` is somehow _bad_. I've shared some of that on the Boost ML too, but this thread seems to be on-point, so I thought I'd ask again here and now. It seems to me that **blocking** the IO thread is bad, but since I don't block and instead post/schedule the actual blocking processing to later, to be dispatched by the `io_context`, isn't that good enough?  
  
It's certainly possible to use multiple threads on the io_context, in effect turning the io_context into a thread pool. I think it's bad practice though because:  
  
- If you queue more long-running tasks on the io_context than you have threads, all your IO will stall (defeating the purpose of asynchronous IO)  
- You now are force to use strands everywhere. IO almost always runs best when it's on one thread.  
- dispatching work to a thread pool provides separation of concerns and means that work can arrive from multiple sources without the program being tied to a single model. You can write the work as a separate code unit that knows nothing about whether it was initiated by an io thread or not.

---

## Comment 7

> Username: ddevienne  
> Created at: 2021-12-16 09:33:39 UTC  
> Updated at: 2021-12-16 10:11:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-995597246  

On your first point, when you dedicate N threads to the server (i.e. the user starts my server with `-j N`), that indicates how much resources the server is supposed/allowed to use. So when the server schedules enough work to use all threads, it basically maxed-out it's compute _budget_, what good is it to receive more work from incoming IO?  
  
Of course, that's not the same at the _other end_ of the IO, I do never want the sending-response part of the IO to stall, that would prevent the work to complete (since the request may be long running, and the _protocol_ allows partial intermediate responses, I actually do that, so a single request message may have many response messages, with a protocol-specific correlation mechanism). The process-msg strand work item posts 1-or-more work items to the encode-and-send-response strand). Is that the real reason WORK items and ASIO should have separate thread-pool? (maybe it's an _ah-ah moment_ for me in fact).  
  
> force to use strands. IO runs best on one thread.  
  
I'm not sure I understand. What's wrong with strands?  
Also, are you saying only 1 thread should be dedicated to ASIO, and my other N-1 threads to WORK? Should those still use strands?  
  
> work can arrive from multiple sources  
  
This is a WebSocket server. What other sources do you have in mind? Like a combined HTTP-REST and WS server? Again, not sure to follow.  
  
In my design, the only thing the client code sees is a pure C++ interface completely independent of ASIO (or the encoding used by the binary WS messages), interface which I have several implementations of, and all the WebSocket and serialization/deserialization using strands is an _implementation detail_. The interface's contract guarantees serial execution of its various methods (thanks to strands), with one instance of that interface per-WS-Connection. Inputs and Outputs of the interface's methods are standalone (no dep) C++ structs, with the Outputs (since not 1-req-to-1-res as mentioned above) _returned_ via a _callback_ passed as another Input (and not the method's return value). So it seems to me that there's already a clear separation of concern. So what am I missing in your point?  
  
Note that I'm not trying to be argumentative, I'm genuinely puzzled by some of the advices here, and since you guys are much more experienced than me I'm guessing (in general probably, but definitely for writing network servers, since my first!), I do really want to understand what I'm missing.

---

## Comment 8

> Username: ddevienne  
> Created at: 2021-12-16 09:43:09 UTC  
> Updated at: 2021-12-16 10:09:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-995604205  

The example from https://github.com/boostorg/beast/issues/2362#issuecomment-995568821 is independent from Beast. What if the work is initiated from a WebSocket message, and the output of the _blocking_ processing is in fact _not-scalar_, but must be send back **continuously** to the WebSocket the request came from? I.e. not only overall throughput matters, but also and just as importantly, _time-to-first-response_, to allow concurrent processing in the client-side of the WS too.  
  
The protocol I'm implementing is actually a v2. The first version only allowed granular requests, leading to too much network-round-trips and pour performance. v2 allows bundling many individual entity requests into fewer and larger WS messages, and we definitely do not want to have to wait for full completion of large requests, but in a way _stream_ the response entities, bundled in multi-entity response messages, to be send back as they become available.  
  
It's hard to see  how to go from your pure-ASIO example, to the above model with actual Beast interaction, I'm afraid...

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-12-16 22:57:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-996257484  

If you want to send back responses piece by piece you may be interested in the new asio::experimental::channel type. Alternatively you could implement your own asynchronous queues or use signal/slot with a send queue.  
  
Often the IO loop will want to concern itself with more than just IO and long-running work. For example, you may want a timeout to cancel the connection if the work takes too long, or the client doesn't send work to do.  
You may have a channel for IPC or interrogating the state of the server, or you may want to handle signals such as sigint and sigterm gracefully. There are many reasons you would want the IO loop to remain functional if the worker threads are fully occupied. Even if you use all the cores in your system for your workers for maximum throughput, it's still nice to have the facility for the server to respond to events on the I/O loop as a result of a pre-emptive context switch.  
  
In most cases the network card is itself a mutex. So using multiple threads to do IO doesn't gain anything. In fact in high performance systems, you will want to pin all IO to one core to avoid context switches. Adding threads and then adding strands (which are nothing more than fancy mutexes) just invites contention.  
  
Strands are of course very useful when there are many times more asynchronous workers than there are cores, and those workers are asynchronously sharing a component.  
  
with respect to mapping my example onto beast, the asynchronous flow would be:  
  
```  
while(continue)  
{  
  read_request (async suspension point)  
  dispatch work to thread pool  
  wait for work to finish (async suspension point)  
  send response (async suspension point)  
}  
```  
  
or a more complex pipelined version of the above

---

## Comment 10

> Username: WarrenN1  
> Created at: 2021-12-19 20:54:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-997459689  

The problem is that fundamentally the synchronous query to MySQL is NOT really doing work. Its send a message to the MySQL application and then the application  does the work in reality while our program waits. My goal is to take advantage of that. I send off the request to the MySQL application and then do some other work and check back when the server gets back to me.

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-12-20 08:16:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-997694033  

> The problem is that fundamentally the synchronous query to MySQL is NOT really doing work. Its send a message to the MySQL application and then the application does the work in reality while our program waits. My goal is to take advantage of that. I send off the request to the MySQL application and then do some other work and check back when the server gets back to me.  
  
Yes that's right, which is why you do the synchronous work on the thread pool while the IO executor remains unblocked.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2022-06-16 14:28:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2362#issuecomment-1157725794  

It looks like this is resolved
