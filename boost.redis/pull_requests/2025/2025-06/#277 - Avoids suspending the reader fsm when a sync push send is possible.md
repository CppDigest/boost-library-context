# #277 Avoids suspending the reader fsm when a sync push send is possible [Closed]

> Username: mzimbres  
> Created at: 2025-06-25 10:39:02 UTC  
> Updated at: 2025-07-04 18:49:57 UTC  
> Closed at: 2025-07-04 18:49:57 UTC  
> Url: https://github.com/boostorg/redis/pull/277  



---

## Comment 1

> Username: anarthal  
> Created_at: 2025-06-26 09:45:04 UTC  
> Url: https://github.com/boostorg/redis/pull/277#issuecomment-3007871732  

I'm not convinced. What is the advantage of avoiding a fsm suspension? AFAIK suspending a FSM does not have any cost (it's not like suspending an Asio async operation, which has a very noticeable cost). On the other hand, creating and invoking a `std::function` is likely more costly than suspending a FSM.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-06-26 10:41:20 UTC  
> Url: https://github.com/boostorg/redis/pull/277#issuecomment-3008031898  

The code to send pushes we had in `reader_op` was  
```cpp  
               if (conn_->receive_channel_.try_send(ec, act.push_size_)) {  
                  continue;  
               } else {  
                  conn_->receive_channel_.async_send(ec, act.push_size_, std::move(self));  
                  return;  
               }  
```  
this creates a ping-pong between the fsm and the `reader_op` which floods the log since the push channel size is 256 by default.  
  
I also believe logging should be restricted to async operations since between suspension and resume a lot of things can happen. Synchronous things don't need logging and are better tested in unit tests.  
  
Also, the suspension to notify the push was located inside [this](https://github.com/boostorg/redis/blob/b82224700e1396b2d588881c6c6c386d8064b412/include/boost/redis/impl/reader_fsm.ipp#L38) loop and from the processor pipeline point of view I am no so sure the cost of suspending and then jumping right back in the loop is so negligible. I would say it is cheaper to call the function.  
  
This is also more in accordance with the strategy of _keeping the async-op stupid and move the code to the fsm_.  
  
I was just not sure about where should I put the `std::function`, whether in the `reader_op` or in the `multiplexer`. I am still considering it, the multiplexer already [notifies](https://github.com/boostorg/redis/blob/b82224700e1396b2d588881c6c6c386d8064b412/include/boost/redis/impl/multiplexer.ipp#L129) the `async_exec` operations via the callback `notify_done`, so I think it would be natural for it to also call try to notify `async_receive` synchronously when a push is available.

---

## Comment 3

> Username: mzimbres  
> Created_at: 2025-06-26 10:50:26 UTC  
> Url: https://github.com/boostorg/redis/pull/277#issuecomment-3008054859  

Regarding the importance I remember at least one use case from @ashtum where Boost.Redis was used to process thousands of pushes per second. This is just to say that performance is important.

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-06-26 13:40:16 UTC  
> Url: https://github.com/boostorg/redis/pull/277#issuecomment-3008541948  

* Regarding code organization, performance aside, the approach closer to what we've been doing would be having a `action::type::try_notify_push_receiver` that triggers a `channel::try_send` and resumes the reader with whatever result the channel yielded.  
* Regarding logging, I think that just logging every action is a flawed approach. As you said, sync actions shouldn't be logged. And we should change the log level of some of the messages depending on errors. This makes logging not dumb, and hence I think it should be done in the reader_fsm, and not in the async_compose loop. This is now possible that logger is no longer a template.  
* Regarding performance, I frankly don't know. Resuming the FSM is just a goto (implemented as a switch/case statement), regardless of whether the position to be resumed is within a loop or not. `std::function` does type erasing (so it's an indirect call) and has handling for NULL values. I'd try doing some metrics before assuming anything here, as we may be hurting performance thinking that we're doing otherwise.  
* If this piece of code is performance-critical, I'd advise making `reader_fsm::resume` inline rather than in the .ipp. This makes it much easier for the compiler to do optimizations.

---

## Comment 5

> Username: mzimbres  
> Created_at: 2025-06-26 17:36:06 UTC  
> Url: https://github.com/boostorg/redis/pull/277#issuecomment-3009245832  

I let the idea ripe a bit more. For now I will move the `try_notify_push` callback to the multiplexer that is where I think it belongs to.  
  
> Regarding code organization, performance aside, the approach closer to what we've been doing would be having a action::type::try_notify_push_receiver that triggers a channel::try_send and resumes the reader with whatever result the channel yielded.  
  
If we take this direction I will also move notify_done out of the multiplexer into the reader_op so we have some consistency. I actually did this but then dropped the commit because it feels like it is the multiplexer role to unblock pending ops when they are ready to continue.

---
