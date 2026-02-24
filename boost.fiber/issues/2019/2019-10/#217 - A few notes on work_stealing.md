# #217 - A few notes on work_stealing [Open]

> Username: lenerd  
> Created at: 2019-10-30 17:06:18 UTC  
> Updated at: 2025-02-27 00:00:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/217  

Hi all,  
  
I wanted to implement a thread pool for executing fibers using the `work_stealing` algorithm from the library. While doing this I ran into a couple of problems. Maybe these are intended limitations of the library but I have not anticipated them by just reading the documentation. So I thought I just post here what I noted while using Boost.Fiber.  
  
* You cannot use multiple instances of work_stealing scheduler concurrently in an application:  
  
  `work_stealing` maintains the list and number of its instances in two `static` class members:  
https://github.com/boostorg/fiber/blob/1bc393b3ba8357787d045f6a13a5f7c0de1769d2/include/boost/fiber/algo/work_stealing.hpp#L39-L40  
Therefore, we cannot have two disjoint groups of threads using `work_stealing` to distribute the fibers among themselves.  
  
* You cannot use multiple instances of work_stealing scheduler sequentially in an application:  
  
  The static member `counter_` is initialized with `0`  
https://github.com/boostorg/fiber/blob/1bc393b3ba8357787d045f6a13a5f7c0de1769d2/src/algo/work_stealing.cpp#L26  
and incremented as soon as a thread arrives  
https://github.com/boostorg/fiber/blob/1bc393b3ba8357787d045f6a13a5f7c0de1769d2/src/algo/work_stealing.cpp#L36-L37  
The value (saved in `id_`) is then used as vector index of the current thread. However, there is no way to reset `counter_` to `0`. Hence, we cannot use `work_stealing` a second time.  
  
* You need to have at least two threads to execute `work_stealing` algorithm:  
  
  Otherwise, the single thread may try to steal from a random thread that is different from itself and cannot succeed.  
  
  It may not make much sense to perform the algorithm with a single thread but this way you cannot just change one parameter in order get a single worker thread performing the tasks.  
  
The first points I have circumvented by adapting the code of `work_stealing` as `pooled_work_stealing` and moving the static variable into a `pool_ctx` class (see [lenerd/fiber_tp](https://github.com/lenerd/fiber_tp)).  
  
* You need to keep the *"main" fibers* running until all fibers managed by the scheduler are executed:  
There is something written in the documentation: [Keep workers running](https://www.boost.org/doc/libs/1_71_0/libs/fiber/doc/html/fiber/worker.html#fiber.worker.keep_workers_running)  
    > In order to keep the worker thread running, the fiber associated with the thread stack (which is called “main” fiber) is blocked. For instance the “main” fiber might wait on a condition_variable. For a gracefully shutdown condition_variable is signalled and the “main” fiber returns. The scheduler gets destructed if all fibers of the worker thread have been terminated.   
      
  Initially I understood this paragraph (and especially the last sentence) in the way that I need to prevent the "pool" to get empty (similarly to using `io_context::work` in Boost.Asio). However, if I understood the behavior correctly, the "main" fiber associated with the thread stack needs to stay alive until all other fibers are completed.  
  
This leads to the next point:  
  
* Assume we have such a pool of worker threads and we have created all fibers that we want to run in this pool. Moreover, let there be a fiber which is currently blocked, e.g., waiting for a future whose value is set by some thread outside of the pool. Before I am allowed to close the pool and let the "main" fibers terminate, I have to be sure that this fiber is also completed.  
  
  Is there a way to find out if there are still (possibly blocked) fibers waiting to get completed?  
  
That's it so far. Thanks for the nice library.

---

## Comment 1

> Username: keryell  
> Created at: 2020-03-11 18:42:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/217#issuecomment-597802600  

While benchmarking this library and diving in a lot of segmentation violations, I think I just hit this `static` member problem...  
  
There is also a similar problem in the NUMA version https://github.com/boostorg/fiber/blob/develop/include/boost/fiber/numa/algo/work_stealing.hpp#L41 and the shared-work scheduler https://github.com/boostorg/fiber/blob/develop/include/boost/fiber/algo/shared_work.hpp#L40

---

## Comment 2

> Username: yotann  
> Created at: 2022-03-10 21:22:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/217#issuecomment-1064516311  

Ran into this today:  
  
> You need to have at least two threads to execute work_stealing algorithm  
  
I tried to create work_stealing with 1 thread, but it hung in `this_fiber::sleep_for()`. This limitation should be documented.

---

## Comment 3

> Username: jmatta1  
> Created at: 2025-02-26 19:39:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/217#issuecomment-2686019626  

So, a while ago I had the same problem with shared work and work stealing schedulers being unable to handle multiple "pools." The method I solved it with was... significantly less elegant, but honestly works well, I copied the algorithm code and added an unused non-type template parameter so that I could number the pools.  
[See this stackoverflow question where I asked the question and then wound up using it.](https://stackoverflow.com/questions/51051481/multiple-shared-work-pools-with-boostfiber)

---

## Comment 4

> Username: keryell  
> Created at: 2025-02-26 23:59:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/217#issuecomment-2686473606  

I have not used this project for quite some time, with my last experiments in https://github.com/triSYCL/triSYCL/tree/master/include/triSYCL/detail/fiber  
Probably next time I need something similar I will use a C++20 co-routine-based library, which is not stack-full but at least more standard.
