# #1893 - Performance over different boost version [Closed]

> Username: shuras109  
> Created at: 2020-04-06 19:41:31 UTC  
> Updated at: 2021-11-21 03:10:15 UTC  
> Closed at: 2020-06-05 14:00:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1893  

Hi there!  
I've recently upgrade my application to work with boost 1.72 instead of 1.69.   
Logic keeps the same. Flat websocket, async operations, strand and one thread per io_context.  
Now it just extensively use beast::bind_front_handler instead of combination from boost::asio::bind_executor, strand and boost::bind.   
And as it turns out new boost version eats 5-10% cpu more. Is it something known? I am just curious.  
At the same time new version should allow to use better buffer technics and probably optimize code beyond old boost version.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-07 11:38:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-610336717  

Hi @shuras109,  
  
It's difficult to comment without seeing the code. There have been a few fundamental changes in ASIO since 1.69 which may account for some of what you're seeing, but my instincts lead me to think that during the conversion process, some pessimisation has crept in to your code (again, difficult to say without seeing it).  
  
You mention one thread per io_context (usually a performance win) but also mention strand in the same descrption. Normally, if an io_context is running on one thread, strands are un-necessary unless there is inter-context communication.  
  
I'd love to be able to help. Are you able to elaborate further?

---

## Comment 2

> Username: shuras109  
> Created at: 2020-04-07 16:16:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-610482342  

My code is far from to be perfect for sure. :)  
And yes there is massive data interexchange between objects running in different single-threaded contexts:  
```  
                    boost::asio::post(  
                                boost::asio::bind_executor(  
                                    assigned_queue_.get_strand(),  
                                    std::bind(  
                                        &queue::put_to_process,  
                                        &assigned_queue_,  
                                        data)));  
```   
This part is not changed a bit during transition to 1.72.  
Whole application is actually just one big exchange hub between instances of boost::asio::posix::stream_descriptor running pipe filedescriptor (each running in dedicated ioc-thread) and instances of boost::beast::websocket (dispersed among a group of ioc-thread).  
I suppose boost::asio is changed big from 1.69 to 1.72 so it is not possible to say where this small degradation occurred and probably it is small price we have to pay for new features, allowing us in perspective to gain more than we lose.

---

## Comment 3

> Username: shuras109  
> Created at: 2020-04-07 20:00:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-610591400  

Which buffer structure will be optimal to transfer data (without any changes to it) between boost::asio::posix::stream_descriptor and boost::beast::websocket and other way around?

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-04-08 10:37:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-610883243  

Oh. This just became an interesting motivating use case.  
  
I have been doing some work on unifying asio dynamic buffers and beast dynamic buffers, but unfortunately it has not made it into the imminent 1.73 release.  
  
Your comment has highlighted the importance of this work.

---

## Comment 5

> Username: shuras109  
> Created at: 2020-04-08 11:27:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-610903338  

Got it! Thank you for efforts in this area!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-04-09 05:21:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-611334378  

Probably you are using `net::executor` which is the polymorphic executor wrapper. This is the default when you upgrade to 1.72, if you don't explicitly name the executor in the socket's template argument list.

---

## Comment 7

> Username: shuras109  
> Created at: 2020-04-09 11:55:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-611488138  

Yes. Executor is not assigned explicitly. Is there a downsides to using `net::executor` executor?

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-04-09 11:59:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-611489576  

Only that it's a polymorphic wrapper, so every post, dispatch and defer against it will involve one more memory fetch. If you've seen a 10% drop in performance, that won't be it.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-04-24 18:02:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-619164353  

>  If you've seen a 10% drop in performance, that won't be it.  
  
Yes actually `net::executor` performs a dynamic allocation upon construction, this means at least one additional dynamic memory allocation per initiating function. It absolutely can cause a 10% decrease in performance. Do not use the polymorphic wrapper if you care about speed.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-04-24 18:06:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-619165803  

...and, there is one dynamic allocation for every `post`, `defer`, or `dispatch` made through the executor - this is very expensive and defeats the recycling allocator built into the io_context:  
https://github.com/boostorg/asio/blob/d114cff2d264f38f7eafbdbb73a76707dbb99942/include/boost/asio/impl/executor.hpp#L47

---

## Comment 11

> Username: stale[bot]  
> Created at: 2020-05-25 04:27:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-633369850  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-05-25 06:44:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-633407195  

> ...and, there is one dynamic allocation for every `post`, `defer`, or `dispatch` made through the executor - this is very expensive and defeats the recycling allocator built into the io_context:  
> https://github.com/boostorg/asio/blob/d114cff2d264f38f7eafbdbb73a76707dbb99942/include/boost/asio/impl/executor.hpp#L47  
  
This is terrible. You have to work pretty hard to not use net::executor in modern asio code.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2020-05-25 14:42:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-633604257  

>This is terrible.   
  
There are plenty of use-cases where this performance difference doesn't matter. For example when the cost of calculating the HTTP response is orders of magnitude greater than the cost of an additional memory allocation.

---

## Comment 14

> Username: madmongo1  
> Created at: 2020-06-05 14:00:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-639501878  

Closing issue, as probably cause is beyond the scope of Beast.

---

## Comment 15

> Username: brjoha  
> Created at: 2021-11-20 15:43:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-974668817  

Just noting that I'd not have known about the performance hit of the default executor used by net::basic_stream_socket and beast::basic_stream if not for stumbling on this thread.  
  
I didn't see it called out in the documentation for either of those classes, and seems it really should be.  
  
Are there any other performance caveats like this to be aware of?

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-11-20 19:07:32 UTC  
> Updated at: 2021-11-20 19:17:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-974697234  

Getting maximum performance from Asio is an art form in itself.  
- Using named executor types is a first step.   
- Then there is the use of custom allocators. If your server has a fixed maximum number of connections for example, you can pre-allocate all memory required.  
- Another thing people do is use a kernel bypass network library,   
- use a single thread with affinity to a dedicated processor,   
- configure the io context to use no mutexes and use io_context poll() rather than run() (this will cause your cpu to hot spin)

---

## Comment 17

> Username: brjoha  
> Created at: 2021-11-20 22:32:30 UTC  
> Updated at: 2021-11-20 22:40:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-974720870  

Thanks for that list above.  Some I knew about and some I did not.  
  
Regarding named executor types, does it mean I need to explicitly name the executor in net::awaitable's as well?  
  
Is there a compile-time or run-time mechanism to change the default executor (system wide) rather than defining my own _using_ types that name my preferred executor?

---

## Comment 18

> Username: madmongo1  
> Created at: 2021-11-20 23:48:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-974727864  

> does it mean I need to explicitly name the executor in net::awaitable's as well?  
  
Yes, once you start specifying a specific executor model it becomes viral.  
  
> Is there a compile-time or run-time mechanism to change the default executor (system wide) rather than defining my own using types that name my preferred executor?  
  
Unhappily no. I tend to write my own config.hpp file which defines the types I need for my program's namespace.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2021-11-21 02:10:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-974743006  

> Is there a compile-time or run-time mechanism to change the default executor (system wide) rather than defining my own using types that name my preferred executor?  
  
That would be problematic for libraries that want to change the setting. Just declare a type, like this:  
  
https://github.com/vinniefalco/BeastLounge/blob/066147b24abe38b2d0e6c80d6fc5d6239e6c4b77/server/core/types.hpp#L28

---

## Comment 20

> Username: brjoha  
> Created at: 2021-11-21 03:10:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1893#issuecomment-974747838  

Yes, I can define those types, as well as resolver, acceptor, etc.  
  
It's viral, like madmongo1 said above, but there doesn't appear to be a better option.
