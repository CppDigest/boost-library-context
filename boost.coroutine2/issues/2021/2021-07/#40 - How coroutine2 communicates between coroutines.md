# #40 - How coroutine2 communicates between coroutines [Closed]

> Username: zchGithub123  
> Created at: 2021-07-15 05:46:20 UTC  
> Updated at: 2021-07-16 02:05:41 UTC  
> Closed at: 2021-07-15 14:10:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/40  

How coroutine2 communicates between coroutines?

---

## Comment 1

> Username: olk  
> Created at: 2021-07-15 07:35:10 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/40#issuecomment-880467777  

I don't understand your question. A coroutine does not communicate with other coroutines.

---

## Comment 2

> Username: unicomp21  
> Created at: 2021-07-15 10:54:21 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/40#issuecomment-880597969  

I think the question is about something similar to a channel in golang.  For example a c++ queue which support co_await on the enqueue/dequeue.  asio supports co_await on tcp socket read/write, might be worth a visit.  @zchGithub123

---

## Comment 3

> Username: olk  
> Created at: 2021-07-15 14:07:51 UTC  
> Updated at: 2021-07-15 14:08:16 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/40#issuecomment-880725046  

co:await is C++20 coroutine specific ... has nothing to do with boost.coroutine2  
  
> I think the question is about something similar to a channel in golang. For example a c++ queue which support co_await on the enqueue/dequeue. asio supports co_await on tcp socket read/write, might be worth a visit. @zchGithub123  
  
co_await is C++20 coroutine specific ... has nothing to do with boost.coroutine2

---

## Comment 4

> Username: olk  
> Created at: 2021-07-15 14:10:22 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/40#issuecomment-880727024  

If you ask how to transfer data between two coroutines than you can simply use any type of container etc.  
coro1 pushes value to a container and coro2 pops values from the container - both coroutines need access to the same container of course.

---

## Comment 5

> Username: unicomp21  
> Created at: 2021-07-15 14:11:55 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/40#issuecomment-880728276  

Right, so is there a similar channel/queue concept for coroutine2?  
  
On Thu, Jul 15, 2021 at 9:08 AM Oliver Kowalke ***@***.***>  
wrote:  
  
> co:await is C++20 coroutine specific ... has nothing to do with  
> boost.coroutine2  
>  
> I think the question is about something similar to a channel in golang.  
> For example a c++ queue which support co_await on the enqueue/dequeue. asio  
> supports co_await on tcp socket read/write, might be worth a visit.  
> @zchGithub123 <https://github.com/zchGithub123>  
>  
> co:await is C++20 coroutine specific ... has nothing to do with  
> boost.coroutine2  
>  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/coroutine2/issues/40#issuecomment-880725046>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAEFL7PTTX6AN5RJN6GZIPLTX3TUHANCNFSM5AM2BNMA>  
> .  
>

---

## Comment 6

> Username: olk  
> Created at: 2021-07-15 14:48:41 UTC  
> Updated at: 2021-07-15 14:50:36 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/40#issuecomment-880757220  

no, because it's out of the scope of boost.coroutine2.   
boost.fiber provides channels... but it utilizes fibers.  
but both libs are based on boost.context.

---

## Comment 7

> Username: zchGithub123  
> Created at: 2021-07-16 02:05:41 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/40#issuecomment-881122891  

@olk @unicomp21 thanks!
