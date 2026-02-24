# #149 - How to implement some worker threads for fibers? [Closed]

> Username: qiaone  
> Created at: 2017-11-03 13:21:50 UTC  
> Updated at: 2017-11-04 19:49:22 UTC  
> Closed at: 2017-11-03 18:42:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/149  

I tried to start a thread and put a scheduler in it, so that the thread sleeps when there's no work, and get the work done when fibers dispatched to the thread. But I found it not so trivial when I wrote:  
  
`auto th = new std::thread([](){  
boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(1,0,true);});  
`  
As the doc says, the 3rd parameter of the _work_stealing_algorithm_ indicates the scheduler will suspend if no ready fiber available, but I found the thread immediately went to the end and destructed.  
  
I can't figure out how to make the scheduler in the thread persists, I was tempted to put a loop in the thread, but I don't know the condition should be checked for the loop.  
  
The Boost.ASIO has io_service::work and dispatch()/post() mechanisms for the above purposes, please tell me is there any equvalents in Boost.Fiber to make the scheduler more useful, since applications typically don't have multiple event loops. After all, the integration of Boost.ASIO with Boost.Fiber with multiple threads and work_stealing schedulers is not working well right now, if not impossible.

---

## Comment 1

> Username: olk  
> Created at: 2017-11-03 14:49:17 UTC  
> Updated at: 2017-11-03 14:58:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/149#issuecomment-341724982  

1) ctor of work_stealing takes two parameters (bost-1.65.1)  
2) the work_stealing alog doesn't share anything with other work_stealing instances -> you need at least two (otherwise from which other work-stealing algo should it steal from?)  
3) the last parameter of work_stealing's ctor indicates that the algo should block if the scheduler runs out of fibers. for instance you could use a condition_variable to wait/suspend the main-fiber of the worker-thread and to signal to terminate.  
4) you start only on fiber (e.g. the main-fiber  == std::thread) which does nothing but terminates. if the main.fiber (==thread) terminates the scheduler gets indication to terminate. so you code simply registers the work_scheduling algo and then terminates -> you need to block the main-fiber  
  
sample code:  
  
```  
boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(2,true);  
boost::fibers::mutex mtx;  
boost::fibers::condition_variable_any cv;  
auto t = std::thread(  
        [&mtx,&cv]{  
            boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(2,true);  
            mtx.lock();  
            cv.wait( mtx);  
            mtx.unlock();  
        });  
std::this_thread::sleep_for( std::chrono::seconds( 2) );  
cv.notify_all();  
t.join();  
```

---

## Comment 2

> Username: qiaone  
> Created at: 2017-11-04 02:44:14 UTC  
> Updated at: 2017-11-04 02:53:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/149#issuecomment-341866420  

Thanks for your kind reply, @olk  
1, I was using an older version so the work stealing alg signature is different, I'll upgrade to the latest.  
2, I do started multiple threads, but I over-simplified the code when I posted the issue. I'm sorry for my ambiguity.  
3, You did gave me the answer I really needed, i.e. using the mutex and cond_var to block and awake.  
4, I still have some difficulty in the following scenario:  
  
**Main (and Graphics) Thread:**  
`while(ok){ fiber f([this](){tick();}).detach();}`  
_Game logic updates and render operations are accomplished in the `tick()`._  
I can't use work stealing scheduler because the graphic context is initialized in the main thread. I can only submit render commands in the main thread (in _tick()_) and unable to use fiber mitigation.  
  
**Worker threads:**  
If the main thread and worker threads are all governed by the work stealing scheduler alg, I know what to do -- just spawn fibers in the _tick()_ and let the idle schedulers steal work from main thread. But since I can't use work stealing scheduler in the main, I don't know how to implement this. Here is the code when using Boost.ASIO:  
  
`class TaskSys{`  
`...`  
`static const int worker_thread_count = 3;`  
`boost::thread_group worker_threads;`  
`boost::asio::io_service worker_service;`  
`boost::asio::io_service::work io_work;`  
`};`  
  
`TaskSys::TaskSys : io_work(worker_service){`  
`for (std::size_t i = 0; i < worker_thread_count ; ++i){`  
`    worker_threads.create_thread([this]() { this->worker_service.run(); });`  
`}}`  
  
_Main thread and worker threads may use different `asio::io_service`_  
Then I can use `worker_service.post(work_callback)` in `tick()` to let the worker threads do the work and get the callback. In fact, I believe using fibers with multiple threads(work stealing schedulers) for computation and using fibers::future<> will improve the runtime efficiency and reduce the chaos of callbacks. But I don't know how to post/dispatch work to the worker threads given the above limitation.  
  
Thanks again for all your help.

---

## Comment 3

> Username: olk  
> Created at: 2017-11-04 08:18:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/149#issuecomment-341880104  

You could install the round_robin scheduler (or equivalent) for the main-thread for graphic ops and thw worker thread install the work-stealing scheduler. In order to pass tasks from main-thread to the worker-threads you could use channel (buffer/unbuffered) or a concurrent-queue.  
In such a scenario you could kepp the fibers running while they dequeue and process tasks from the queue/channel.  This prevents too much memory allocations.  
The asio::io_service is in fact a task-/work-queue.

---

## Comment 4

> Username: qiaone  
> Created at: 2017-11-04 16:43:24 UTC  
> Updated at: 2017-11-04 16:48:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/149#issuecomment-341911317  

Thanks for your kind reply. @olk   
I tried to implement it using the code below. But the executable almost always crashes. I tested the code with three computers(win8.1 vs2017 laptop, win8.1 vs2015 desktop, win7 vs2017 desktop), with no additional compiler flag. In fact, `performance/fiber/skynet_shared_detach.cpp` also crashes in my desktop. After hours of trial and error, I still have no clue to solve the problem. The crash point varies from one run to another, but most scenarios do related with the scheduler like the screenshot below. Could you offer some insight into the problem?  
  
```  
const int test_fiber_thread_count = 10;  
boost::thread_group test_fiber_threads;  
boost::fibers::mutex mtx;  
boost::fibers::condition_variable_any cv;  
moodycamel::ConcurrentQueue<std::function<void(void)>> wq;  
boost::fibers::barrier barrier1(test_fiber_thread_count);  
boost::fibers::barrier barrier2(test_fiber_thread_count);  
  
void init()  
{  
	for (std::size_t i = 0; i < test_fiber_thread_count; ++i)  
	{  
		test_fiber_threads.create_thread([i]() {  
			barrier1.wait(); // the barriers were added to debug the problem, but no luck  
			boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(test_fiber_thread_count, true);  
			barrier2.wait();  
			while (true)  
			{  
				std::function<void(void)> f;  
				if (wq.try_dequeue(f))  
				{  
					boost::fibers::fiber fib(f);  
					fib.detach();  
				}  
				else  
				{  
					mtx.lock();  
					cv.wait(mtx);  
					mtx.unlock();  
				}  
			}  
		});  
	}  
}  
```  
  
![devenv_2017-11-05_00-34-28](https://user-images.githubusercontent.com/5222364/32407250-2f04444e-c1c1-11e7-862e-cf44e6decff1.png)

---

## Comment 5

> Username: olk  
> Created at: 2017-11-04 19:16:38 UTC  
> Updated at: 2017-11-04 19:49:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/149#issuecomment-341922735  

I recommend to use the code from branch develop.  
Maybe the stack of the fiber is not big enough - try to create the fibers with a big stack (for isntance you could start with 1MB).
