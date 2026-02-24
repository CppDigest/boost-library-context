# #174 - bounded_fifo_queue with runtime size [Closed]

> Username: ropieur  
> Created at: 2021-12-16 14:36:00 UTC  
> Updated at: 2021-12-18 12:17:57 UTC  
> Closed at: 2021-12-16 17:40:05 UTC  
> Url: https://github.com/boostorg/log/issues/174  

Hi,  
Digging into the library I found a bounded_fifo_queue with a compile time size.  
I need to deliver an application where we can customize the size of queue depending on which platform it is delivered.  
Is there an equivalent with the size set at runtime? Or should I code it ?  
Thank you

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-16 17:40:05 UTC  
> Url: https://github.com/boostorg/log/issues/174#issuecomment-996036354  

> Is there an equivalent with the size set at runtime?  
  
No, there isn't one.  
  
What is the use case that requires runtime configuration?

---

## Comment 2

> Username: ropieur  
> Created at: 2021-12-18 09:38:46 UTC  
> Updated at: 2021-12-18 09:39:00 UTC  
> Url: https://github.com/boostorg/log/issues/174#issuecomment-997176954  

> What is the use case that requires runtime configuration?  
  
Different system dimensions (the application is scalable)

---

## Comment 3

> Username: Lastique  
> Created at: 2021-12-18 12:17:55 UTC  
> Url: https://github.com/boostorg/log/issues/174#issuecomment-997194482  

The limit effectively sets the maximum delay between the log record is made and processed by the sink backend. If your application is logging intensively, you should choose at which point it should block to ensure the records aren't lost. This can happen at any scale at any time, and you should assume it will happen at the least convenient moment.  
  
If you don't want the queue to block, ever, then use `unbounded_fifo_queue`. But then you may exhaust your RAM and will probably lose a lot of log records should your application crash. Otherwise, select a queue capacity that works reasonably well for the case when you're logging most intensively and use it unconditionally. This is a tradeoff you have to make between logging reliability and performance, and application scale isn't really a factor in this decision.
