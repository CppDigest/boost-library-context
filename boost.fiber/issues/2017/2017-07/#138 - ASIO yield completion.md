# #138 - ASIO yield completion [Closed]

> Username: klmurphy72  
> Created at: 2017-07-24 17:59:36 UTC  
> Updated at: 2018-01-06 01:38:02 UTC  
> Closed at: 2018-01-06 01:38:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/138  

I believe I may have found a race condition pertaining to the ASIO completion handler.  
  
In the current implementation, a fiber may be scheduled when it was never suspended.  
  
the check:         `        if ( fibers::context::active() != ctx_) {`  
in yield_handler_base::operator() is in no way a guarantee that a fiber needs to be rescheduled.  
  
I have solved this issue in my local implementation by implementing the following:  
``` c++  
                 struct yield_completion {  
  
			typedef fibers::detail::spinlock                    mutex_t;  
			typedef std::unique_lock< mutex_t >                 lock_t;  
			typedef boost::intrusive_ptr< yield_completion >    ptr_t;  
  
			std::atomic< std::size_t >		use_count_{ 0 };  
			mutex_t							mtx_{};  
			enum complete_state {init, waiting, complete};  
			complete_state								completed_{ init };  
  
			void wait() {  
				// yield_handler_base::operator()() will set completed_ true and  
				// attempt to wake a suspended fiber. It would be Bad if that call  
				// happened between our detecting (! completed_) and suspending.  
				// If completed_ is already set, we're done here: don't suspend.  
  
				lock_t lk{ mtx_ };  
  
				if (!(completed_ == complete)) {  
					// suspend(unique_lock<spinlock>) unlocks the lock in the act of  
					// resuming another fiber  
  
					completed_ = waiting;  
  
					fibers::context::active()->suspend(lk);  
				}  
			}  
`  
  
`  
                        // completion callback passing only (error_code)  
			void operator()(boost::system::error_code const& ec) {  
				BOOST_ASSERT_MSG(ycomp_,  
					"Must inject yield_completion* "  
					"before calling yield_handler_base::operator()()");  
				BOOST_ASSERT_MSG(yt_.ec_,  
					"Must inject boost::system::error_code* "  
					"before calling yield_handler_base::operator()()");  
				// If originating fiber is busy testing completed_ flag, wait until it  
				// has observed (! completed_).  
  
				yield_completion::lock_t lk{ ycomp_->mtx_ };  
  
				auto status = ycomp_->completed_;  
  
				// Notify a subsequent yield_completion::wait() call that it need not  
				// suspend.  
				ycomp_->completed_ = yield_completion::complete;  
				// set the error_code bound by yield_t  
				*yt_.ec_ = ec;  
  
				// unlock the lock that protects completed_  
				lk.unlock();  
  
				// If ctx_ is still active, e.g. because the async operation  
				// immediately called its callback (this method!) before the asio  
				// async function called async_result_base::get(), we must not   
				// schedule it, because it was suspended.  
				if (status == yield_completion::waiting) {  
					// wake the calling fiber  
					ctx_->get_scheduler()->schedule_from_remote(ctx_);  
//					fibers::context::active()->schedule(ctx_);  
				}  
			}  
```  
Also, see the last line of code above?  I changed it from:  
``` c++  
				fibers::context::active()->schedule(ctx_);  
```  
to:  
``` c++  
					ctx_->get_scheduler()->schedule_from_remote(ctx_);  
```  
  
This change means that nothing fiber/scheduler wise is created in an thread running io_service::run();  
  
I believe I found where this same change can be implemented to condition_variable and the result would be to notify a condition from a non-fiber thread with little performance penalty.  
  
  
I know this is a simple description.  I can attempt to explain more if necessary.  But, right now my head hurts so bad right now from figuring this out!!!  
  
Thoughts?

---

## Comment 1

> Username: olk  
> Created at: 2017-07-30 10:02:36 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-318891262  

I don't see why `ctx_->get_scheduler()->schedule_from_remote(ctx_);` should be better?  
There is no need to call `schedule_from_remote()` because the handler is always called from the same thread.  `schedule_from_remote()` does aquire a lock, which is unnecessay if `yield_handler_base::operator()` is called from the thread that owns the fiber (== the fiber is associated with).

---

## Comment 2

> Username: olk  
> Created at: 2017-07-30 10:27:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-318892528  

Please explain why `if ( fibers::context::active() != ctx_)` is insufficient.

---

## Comment 3

> Username: klmurphy72  
> Created at: 2017-07-31 16:41:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319127404  

I don't know if it matters but my asio threads are NOT fibers.  They are totally external.  This means that  
```ctx_->get_scheduler()->schedule_from_remote(ctx_); ``` does not cause a scheduler to be created in the asio run thread.  Actually,``` fibers::context::active()->schedule(ctx_) ```  just does some extra checks and then calls ```ctx_->get_scheduler()->schedule_from_remote(ctx_); ```  
  
```fibers::context::active()``` will always create a scheduler for the current thread.  I am avoiding that where not necessary.  
  
The locking problem occurs because the fiber thread starts the operation and the asio thread runs the handler to completion before the wait operation in the fiber thread even has a chance to start.  The thread was never suspended.....and therefore can not be rescheduled.  
  
My application works by starting a fiber loop in a thread and other threads that runs asio.run.  I then generate ASIO requests with a yield_t from the fiber thread.  There is no need for a scheduler in the asio.run thread(s).  
  
You said:  
  
> I don't see why ctx_->get_scheduler()->schedule_from_remote(ctx_); should be better?  
> There is no need to call schedule_from_remote() because the handler is always called from the same > > thread. schedule_from_remote() does aquire a lock, which is unnecessay if > yield_handler_base::operator() is called from the thread that owns the fiber (== the fiber is associated with).  
  
"Always called from the same thread?"  ???????      No!!!  It is indeed being called from another thread!  As stated earlier.....even the original approach called schedule_from_remote.   
  
Thank you for your time!!!

---

## Comment 4

> Username: olk  
> Created at: 2017-07-31 17:42:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319141666  

> I don't know if it matters but my asio threads are NOT fibers.  
Makes no sense to me - fibers run in threads, but aren't threads  
  
I don't understand your use case!  
The asio binding is provided to use fibers together with asio's io_service. The application must be single threaded (multithreading doesn't work because of some missign guarantees from asio's io_service).  
  
Seams to me you have an invalid use case.

---

## Comment 5

> Username: klmurphy72  
> Created at: 2017-07-31 17:54:19 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319145298  

Invalid use case?  OK.  I may not be using it the way you had planned, but this does work.  Multiple threads running ios.run.  I can also have multiple threads running fibers.  
  
Integration between fibers and threads occur only through the yield handler.  I am not using the provided example asio "round_robin" algorithm.  
  
With the provided change to the yield handle I can do an asio.run per thread for each core.  And, I can have multiple fiber threads all using this I/O service.  It works amazing!!!!!!!  
  
Maybe I am integrating asio a little differently.  I get better performance this way.  
  
Would it help if I provided a small example?  
  
Thank you!!!

---

## Comment 6

> Username: olk  
> Created at: 2017-07-31 18:03:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319147978  

an example would be helpful

---

## Comment 7

> Username: klmurphy72  
> Created at: 2017-07-31 18:10:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319150286  

Sorry, I have some more thoughts.  
  
This approach I am using would be analogous to asio's "use_future" handler.  I believe.  
  
I understand the limitations of waking an asio thread.  This approach mitigates this problem by integrating differently.  
  
I have not analyzed the changes I have made to the yield handler if used in the current case.  It may not work.  Maybe what I have created is new integration path with similar but different requirements for the yield handler?

---

## Comment 8

> Username: olk  
> Created at: 2017-07-31 18:27:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319155168  

please provide an example

---

## Comment 9

> Username: klmurphy72  
> Created at: 2017-07-31 20:20:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319184791  

Example attached:  
  
[FiberAsioExample.zip](https://github.com/boostorg/fiber/files/1188723/FiberAsioExample.zip)  
  
This example does the following:  
  
1.  Create a fiber to run an echo server  
2.  Create a fiber to monitor(display) the number of successful echo received    
2.  Creates NUM_CPUS thread and runs ios.run() in each.  
3.  Creates NUM_CPUS threads and starts a fiber in each  
    * Each of these fibers connects NUM_CLIENTS_PER_CPU echo clients to the echo server  
  
  
Wham Bam!!!!!

---

## Comment 10

> Username: olk  
> Created at: 2017-08-01 17:56:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319447434  

Your code does not work if you have a mixture of asio tasks **and** fibers that are unrelated to asio.  
  
The threads running io_service::run() would block if io_service' task queue becomes empty.  
For instance: if a fiber is blocked in fibers::condition_variable::wait(), it will **not** be resumed if the condition_variable gets signaled from another thread (calling fibers::condition_variable::notify_one()). That's because io_service doesn't know fibers (how to resume fibers).  
  
The problem that has to be solved is, that two event dispatchers (io_serivce **and** fiber-scheduler) must be coordinated (not blocking the other one).

---

## Comment 11

> Username: klmurphy72  
> Created at: 2017-08-01 19:15:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319468844  

We don't seem to be communicating well.  I'm sorry for my part.  
  
Could you please clarify your first comment:  
> Your code does not work if you have a mixture of asio tasks and fibers that are unrelated to asio.  
  
Are saying that a fiber-blocking operation within is.run() would be problematic?  If yes....I agree.  
  
But, the code presented does not do that.   
  
> The problem that has to be solved is, that two event dispatchers (io_serivce and fiber-scheduler) must be coordinated (not blocking the other one).  
  
I have coordinated them.  All asio calls are handled by the asio threads and then continued back in the fiber threads upon completion.  
  
I see my attached example as proof that fibers and asio can exist together in harmony.  There is a limit however, in that you cannot do a blocking fiber operation within a thread doing asio.run(), i.e. an asio callback.  
  
The limit to your approach is that you can have only one thread running asio.  Correct?  
  
Thank you!

---

## Comment 12

> Username: olk  
> Created at: 2017-08-01 20:17:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319483801  

First, asio gives no guarantee which one of the threads that run io_service::run() deques the next item from io_service' work-queue and executes it. But fibers, doeing an async.-op must be executed by the thread which fiber-scheduler owns this fiber.  
  
Second, a thread that runs io_service::run() will block if io_service' work-queue becomes empty - the thread will only wake up if some work-queue becomes not empty.  
  
Third, if your thread handles asio-related work and fibers that do not deal with asio at all, you need a mechanism that manages events inside ioservice and fiber-scheduler, e.g. if io_service' work-queue becomes empty do not block the thread while the fiber-scheduler has still some fibers ready to be resumed.  
  
The scheduler provided in the example directory can deal with asio-attached fibers (executing asio async.-ops) as well fibers that are not asio related at the same time. It's limitation is, that io_service::run() must not executed by multiple threads (-> only single threaded). This limitation has its root in the fact that work items in the work-queue could be executed by any thread that run io_service::run().

---

## Comment 13

> Username: klmurphy72  
> Created at: 2017-08-01 22:03:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319510347  

Well, I tried to create a new example to demonstrate the original problem I was having.  In the process of doing this I downloaded fresh copies of yield.hpp and detail/yield.hpp to pull into my local project.  
  
After running.......yes, it ran........I was very surprised!  I then went to insert breakpoints near the code I had modified before to fix the problem.   Low and behold!!!!!!  The change I suggested days ago has been implemented!!!!  Thank you!!!!!!  
  
I wish you had told me that the change I had suggested was actually necessary and accepted.  
  
With the change you implemented 2 days ago my use case is valid and functional.  I don't even need the round_robin scheduler in the io_service.run() thread!!!!   
  
A day ago you were telling me I had an invalid use case.  This was a day after you implemented my change!!!!   
  
Awesome,  thank you.

---

## Comment 14

> Username: klmurphy72  
> Created at: 2017-08-01 22:06:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319510924  

Would it be premature to say that yield.hpp could by moved out of examples?  As it exists, it is functional for my use case without the "round_robin" asio scheduler.

---

## Comment 15

> Username: olk  
> Created at: 2017-08-02 08:24:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-319604037  

If I get the fiber scheduler working together with asio's io_service in a multithreaded env, then I move the code to the public API.

---

## Comment 16

> Username: niekbouman  
> Created at: 2017-08-17 11:47:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-323047977  

Hello @klmurphy72 ,  
  
> With the provided change to the yield handle I can do an asio.run per thread for each core. And, I can have multiple fiber threads all using this I/O service. It works amazing!!!!!!!  
>   
> Maybe I am integrating asio a little differently. I get better performance this way.  
  
I am very much interested in your approach for multi-threaded boost::fiber-asio integration.  
Could you comment more about details of your performance measurements?

---

## Comment 17

> Username: klmurphy72  
> Created at: 2017-08-30 20:12:36 UTC  
> Updated at: 2017-08-30 20:41:19 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-326105263  

@niekbouman....Sorry for the late reply.  I have been very busy working out of town lately.  
  
Have you looked at the example code above?  It may help explain a lot!  
  
Basically, I do NOT create a fiber scheduler inside the threads that perform calls to asio's io_service::run() method.  A default "round_robin" scheduler will be created for each thread making this call (at the time a yield_t handler completes).....but the scheduler created is never used.  
  
Asio calls can then be made from other threads utilizing fibers and the  asio yield_t handler from the examples.  Please download the yield handler from github...yield.hpp.  The one provided with 1.65 is broken.....see above.    
  
With this setup, you can indeed have multiple threads utilizing fibers in addition to multiple threads that perform io_service::run() calls.  
  
There is a limitation though.  Fiber blocking operations CANNOT be performed within any asio handlers.  This may cause a deadlock!!!  In my application this is not an issue.  This may be a deal breaker for many.  I don't know.  
  
I think that my code that uses asio is written much cleaner and easier to understand because I don't end up in callback hell.  
  
I hope this helps a little.......I'd love to help more if needed.  
  
Ken

---

## Comment 18

> Username: kirillv  
> Created at: 2017-10-23 12:36:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-338644680  

@olk Is any progress in fiber scheduler working together with asio's io_service in a multithreaded env? Or this is not in roadmap? Thanks in advance.

---

## Comment 19

> Username: olk  
> Created at: 2017-10-23 12:41:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-338646073  

unfortunately no - I've not the time at the moment

---

## Comment 20

> Username: niekbouman  
> Created at: 2017-10-24 14:50:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-339016836  

Dear @olk , and others,  
  
First, thank you @olk for your work on Boost.Fiber (and other Boost libs)!  
  
I have a question related to this discussion:  
In [1], Kohlhoff discusses (in Section 6) two ways to apply ASIO in a multi-threaded context:  
  
1. only one io_service, that is called from multiple threads.   
2. multiple io_services: namely one io_service for each thread.   
  
It seems to me that you mean mode 1 when you say that "...asio gives no guarantee which one of the threads that run io_service::run() deques the next item from io_service' work-queue and executes it"  
  
My question is: Could it be that Boost.Fiber's yield.hpp implementation is already compatible with mode 2 (one io_service for each thread), without requiring changes to the way in which asio executes its work?  
  
Kind regards,  
Niek  
  
[1] Christopher Kohlhoff _"Using ASIO with C++11"_, N3388=12-0078, 2012  
[http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3388.pdf](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3388.pdf)

---

## Comment 21

> Username: olk  
> Created at: 2017-10-25 09:54:37 UTC  
> Updated at: 2017-10-25 09:55:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/138#issuecomment-339277884  

variant 2 (one io_service for each thread) + without migrating fibers should work (see example section)  
variant 1 should theoretically work if each fiber is detached after suspension + attached when resumed
