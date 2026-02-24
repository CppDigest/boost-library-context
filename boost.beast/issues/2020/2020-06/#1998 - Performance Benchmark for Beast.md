# #1998 - [DOC] Performance Benchmark for Beast? [Closed]

> Username: scm-ns  
> Created at: 2020-06-25 14:11:46 UTC  
> Updated at: 2020-07-01 18:23:05 UTC  
> Closed at: 2020-07-01 18:23:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1998  

Not an issue. I was wondering if there where any metrics on beast compared to other webservers?   
  
Number of requests/sec etc? Something like the Benchmark performance found here : https://github.com/ipkn/crow. Would be very useful when making design decisions   
  
While we are on the topic of performance, is there a resolution to this issue : https://github.com/boostorg/beast/issues/1893?   
How can we avoid the penalty for the polymorphic allocation. The solution suggested there didn't seem clear to me.   
  
Thanks for the library!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-25 14:12:45 UTC  
> Updated at: 2020-06-25 14:13:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1998#issuecomment-649569286  

> I was wondering if there where any metrics on beast compared to other webservers?  
  
Nah. Other people can run these benchmarks and publish them. The performance of your webserver will largely depend on your own code, and how you process requests - which is outside the scope of Beast (which just provides the low-level HTTP protocol bits).  
  
> How can we avoid the penalty for the polymorphic allocation.   
  
Don't use the polymorphic executor.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-06-25 14:25:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1998#issuecomment-649580719  

> How can we avoid the penalty for the polymorphic allocation. The solution suggested there didn't seem clear to me.  
  
Firstly, as of boost 1.74, (out soon) I believe Chris has solved the performance problem with polymorphic executors. The new polymophic executor will be spelled `asio::any_io_executor`. This version does not perform type erasure. (I think internally it uses flags). This allows the compiler to get a much better view on optimisation and crucially, the use of the winding allocator associated with strands.  
  
Secondly, you can avoid polymorphic executors altogether.  
  
For example a socket with a polymorphic executor is spelled:  
```  
asio::ip::tcp::socket;  
```  
  
This is a synonym for:  
```  
asio::basic_stream_socket<asio::ip::tcp, asio::executor>;  // or asio::any_io_executor in 1.74 and above  
```  
  
You can easily change the default executor type:  
```  
asio::basic_stream_socket<asio::ip::tcp, asio::io_context::executor_type>;  // not protected by strand by default  
asio::basic_stream_socket<asio::ip::tcp, asio::strand<asio::io_context::executor_type>>;  // all operations protected by strand by default  
```  
  
There are similar synonyms for all asio's types, e.g.:  
  
```  
asio::basic_waitable_timer<std::chrono::steady_clock, asio::wait_traits<std::chrono::steady_clock>, asio::io_context::executor_type>;  
asio::awaitable<void, asio::strand<asio::io_context::executor_type>>;  
```  
  
and so on.  
  
I can confirm from personal experience in the finance industry that asio on a properly configured machine is extremely performant and free of jitter (which is a more important issue in actual fact).

---

## Comment 3

> Username: scm-ns  
> Created at: 2020-06-27 08:51:35 UTC  
> Updated at: 2020-06-27 08:51:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1998#issuecomment-650525734  

@madmongo1  Thank you very much. I am able to get upto 80,000 req/s on my local machine, but it depends a lot on the message that I am sending back. I have seen it drop down to 40,000 for some end points with more data being passed.   
I am wondering if I am managing/writing into the buffers properly.   
Are there any links to performance tips/examples/other projects?   
  
Links to benchmarks :   
https://github.com/scylladb/seastar/issues/522  
https://github.com/Qihoo360/evpp/blob/master/docs/benchmark_throughput_vs_asio.md  
  
I think benchmarks would be very useful, especially when it involves comparing to other popular languages. Seeing how many requests can be handled on a single machine, and the associated cost savings could convince more people to use beast. Which I imagine is a goal of this project.   
  
Also a question : Is this complaint still valid? What do people do to get around that?  
https://stackoverflow.com/questions/1234750/c-socket-server-unable-to-saturate-cpu/1238315#1238315  
  
Thanks!

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-06-27 09:56:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1998#issuecomment-650534357  

Hi @scm-ns ,  
  
The stack overflow answer you link is over ten years old. I suspect a lot has changed in Asio since then. Asio is used in over 100 financial exchanges around the world. If performance were an issue, I'm sure Chris would be aware of of it and take action.   
  
In my experience in the finance industry, Asio has never been the bottleneck. Most servers spend all their time encoding/decoding doubles. The actual computation and networking is normally the least expensive part.  
  
Are you able to provide a github/lab link to your test harness. I would be interested to run the same benchmarks locally and see if there are improvements that can be made to either the code or the methodology.   
  
I would expect a well-written server running on a properly configured host to consume very little CPU time when under load. Most of the work should be pushed to the NIC.
