# #237 - Is there a way to list fibers and and switch to stack of a fiber in gdb? [Closed]

> Username: lowsfer  
> Created at: 2020-01-16 17:47:43 UTC  
> Updated at: 2020-01-18 13:46:27 UTC  
> Closed at: 2020-01-18 13:46:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/237  

I'm using boost.fiber to implement a fast 3D reconstruction software, and got a dead lock issue with very low chance (~1 to 3 times if I run the program for one day).  
  
With GDB I see every worker thread is blocked in work-stealing scheduler, indicating that there is no ready fiber to run. I would like to list all fibers and switch to them in GDB, to find out where each fiber is blocked at.  
  
Is there a way to do this? Even a hacky way is acceptable to me. Lack of debugging support is a big problem for practical projects. folly.fiber claims that it has gdb support for this, but boost.fiber is much better for my CPU-intensive task as it has cross-thread migration, work stealing, etc.

---

## Comment 1

> Username: olk  
> Created at: 2020-01-16 20:07:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/237#issuecomment-575324441  

unfortunately there is no GDB support :^(  
you could add some  debug code that generates some output in order to track the problem

---

## Comment 2

> Username: lowsfer  
> Created at: 2020-01-17 03:58:21 UTC  
> Updated at: 2020-01-17 03:59:37 UTC  
> Url: https://github.com/boostorg/fiber/issues/237#issuecomment-575453929  

Yeah, if there is no gdb support, logging is probably the best I can do.  
  
Is this in your roadmap? I think this is an important feature. Without an easy way to debug, it's quite risky to use in a complex project. For bugs that are difficult to reproduce, you have to investigate a ton of logs. Since folly.fiber has it, I think it should be technically possible.

---

## Comment 3

> Username: olk  
> Created at: 2020-01-17 08:07:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/237#issuecomment-575520580  

I'm very limited in time and at the moment I'm focused at other topics - so any help (pull requests) are appreciated

---

## Comment 4

> Username: lowsfer  
> Created at: 2020-01-18 12:58:36 UTC  
> Updated at: 2020-01-18 12:59:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/237#issuecomment-575896378  

My problem is fixed (after ~2 weeks). It's because a throw caused exit of a worker thread main fiber, and it went silently as the thread was started with std::async instead of std::thread. GDB support for fiber won't help me much in this case. I should have tried "catch throw" earlier, instead of interactively adding logs here and there and pray that it will reproduce with useful information within tolerable time.  
  
That said, gdb support should still be quite useful for real dead locks. I'll try to learn how folly.fiber does it, and see if I can make it for boost.fiber. But I'm not very confident about that, as I'm not that good in gdb.

---

## Comment 5

> Username: olk  
> Created at: 2020-01-18 13:46:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/237#issuecomment-575899775  

>  I'll try to learn how folly.fiber does it, and see if I can make it for boost.fiber.  
  
that would be really nice.
