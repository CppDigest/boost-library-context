# #267 - Question about the processing loop in boost::fibers::asio::round_robin [Closed]

> Username: bitbugprime  
> Created at: 2020-12-30 00:27:23 UTC  
> Updated at: 2020-12-30 13:55:23 UTC  
> Closed at: 2020-12-30 13:55:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/267  

Assume I am the last running fiber for the thread executing io_context->run(), the same io_context that round_robin posted a handler-processing lambda to. What must that fiber do to ensure the "while (!io_ctx_->stopped()) {" loop exits when it exits, which should release the thread from run() since it's the last handler? I've tried to just exit the fiber for a clean shutdown but that merely seems to hang my program.

---

## Comment 1

> Username: olk  
> Created at: 2020-12-30 06:06:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/267#issuecomment-752340086  

Not sure about your code - at least if a fiber is waiting/blocked for some event it must be woken up.

---

## Comment 2

> Username: bitbugprime  
> Created at: 2020-12-30 12:29:43 UTC  
> Updated at: 2020-12-30 12:37:07 UTC  
> Url: https://github.com/boostorg/fiber/issues/267#issuecomment-752522028  

> Not sure about your code - at least if a fiber is waiting/blocked for some event it must be woken up.  
  
How does the lambda loop ever exit? It looks like there is either a pending timer created by suspend_until which needs to be handled when it wakes up the main fiber suspended in branch A or, if all fibers other than the main fiber running the lambda loop have exited, in branch B run_one() will wait forever for work to execute, but there will never be work to execute because there are no fibers left to create that work.  
  
I'm assuming the main fiber doesn't count for purposes of has_ready_fibers().

---

## Comment 3

> Username: olk  
> Created at: 2020-12-30 12:57:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/267#issuecomment-752560224  

`io_ctx_->stopped()` will return `true` if `io_ctx_->stop()` has been called.

---

## Comment 4

> Username: bitbugprime  
> Created at: 2020-12-30 13:16:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/267#issuecomment-752583745  

> `io_ctx_->stopped()` will return `true` if `io_ctx_->stop()` has been called.  
  
So to get a clean shutdown the last running fiber needs to ensure all other fibers have exited, then call stop() just before it exits?

---

## Comment 5

> Username: olk  
> Created at: 2020-12-30 13:23:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/267#issuecomment-752592742  

Take a look at the examples.
