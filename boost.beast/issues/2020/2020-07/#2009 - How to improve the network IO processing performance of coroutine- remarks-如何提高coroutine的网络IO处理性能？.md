# #2009 - How to improve the network IO processing performance of coroutine? remarks:如何提高coroutine的网络IO处理性能？ [Closed]

> Username: MechanicsMingYan  
> Created at: 2020-07-07 15:12:05 UTC  
> Updated at: 2020-08-09 13:55:18 UTC  
> Closed at: 2020-08-09 13:55:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2009  

I am using translation software, if the semantic is not clear, please understand!  
  
I'm using "toast" with "net:: yield context"  
However, the concurrent processing capability of TCP / SSL is not ideal, about 4K  
However, using asynchronous mode can achieve 15K +, which makes me wonder. Is there any way to improve the processing performance of coprocessors?

---

## Comment 1

> Username: MechanicsMingYan  
> Created at: 2020-07-07 15:17:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2009#issuecomment-654935700  

I'm using "boost:: ASIO:: spawn" to create a task

---

## Comment 2

> Username: mzimbres  
> Created at: 2020-07-07 15:57:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2009#issuecomment-654960208  

Disclaimer: I am not a maintainer of Beast, just a user.  
  
In most cases the performance penalty of coroutines is not measurable (as compared to callbacks for example). TLS has also a negligible impact on performance, most of the time.  
  
It is difficult to say what is causing poor performance in your case without details. To begin with  
  
* How many threads are you using?  
* How many io_context?  
* How many threads are calling io_context::run()  
* What does 4k and 15k exactly mean?  
* Your code is fully asynchronous or you also have blocking code?  
  
You can compare for example single vs multiple threaded performance for example.

---

## Comment 3

> Username: MechanicsMingYan  
> Created at: 2020-07-10 03:56:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2009#issuecomment-656468359  

The maximum processing power of my program tested under various threads  
The 4K and 15K values are the number of SSL tasks that can be completed per second.  
I have tried pure coroutine, accept asynchronous and pure asynchronous with task coroutines, and found that asynchronous processing power is much higher than that of coroutines, which is puzzling to me.  
I tried single io_ Context is not ideal for both coroutine and asynchrony. At present, I am using three IO_ Context object, when the number of threads is the same as 10 threads.  
My code is completely asynchronous and non blocking, except accept

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-08-09 08:05:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2009#issuecomment-671021677  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: MechanicsMingYan  
> Created at: 2020-08-09 13:55:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2009#issuecomment-671054950  

The problem has not been solved. I have changed the scheme. Please close it for the time being. Thank you for your answer.
