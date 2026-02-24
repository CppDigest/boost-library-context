# #89 - Scheduler redesign proposal [Closed]

> Username: ishitatsuyuki  
> Created at: 2016-09-30 10:21:11 UTC  
> Updated at: 2022-09-18 14:39:50 UTC  
> Closed at: 2022-09-18 14:39:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/89  

The scheduler currently has the following issues for my design:  
- [ ] Algorithm is called even after main fiber is destructed, making it impossible to depend on stack-based data  
- [ ] No way to do a collaborative scheduler, since fibers are locked too much on one thread (scheduler)  
  
The fix proposal is:  
- Making the fibers more like std::thread, which defines a crash if any thread remains after main() exit. In that case, we don't need to do any call to scheduler when destructing.  
- Move timers, remote ready functionality to algorithm, possibly with a pre-implemented virtual function

---

## Comment 1

> Username: olk  
> Created at: 2016-11-27 11:01:44 UTC  
> Updated at: 2016-11-27 11:03:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-263115714  

- the user is responsible for the scheduling-algorithm, of course an note should be added in the documentation  
- fibers != threads, e.g. a fiber will always belong to a thread (tuns in the context of a thread)  
- fiber can be migrated/moved between threads  
  
 > std::thread, which defines a crash if any thread remains after main() exit  
  
does not produce crashes:  
`int main() {  
      std::thread([](){  
          std::cout << std::this_thread::get_id() << ": sleeping" << std::endl;  
          std::this_thread::sleep_for( std::chrono::seconds( 1) );  
      }).detach();  
      std::this_thread::sleep_for( std::chrono::seconds( 1) );  
      std::cout << "main() returns" << std::endl;  
      return 0;  
}`  
  
- only detached fibers remain in the scheduler while terminating/returning from main()  
- of course std::terminate() could be called if fibers are pending while entering ~scheduler()  
- what should be the benfit of mving timers, remote-ready-queue to algorithm?

---

## Comment 2

> Username: ishitatsuyuki  
> Created at: 2016-11-27 11:09:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-263116017  

- I mean, algorithm should be bound to main fiber stack, since it's the entrypoint and the exit point  
  Thus, it shouldn't be touched after main fiber is terminated  
  Details: I'm passing a non-static shared scheduler data to algorithm. shared_work is static based so it's not affected.  
- Remote ready queue is useless if you're doing a multi-threaded algorithm (locking is required regardless local or remote).  
- Sleep queue should be shared between threads so we don't need to wait for scheduler kicking in the target thread  
  Consider the following case:  
  Thread A is running a long job and has something near to expire in the sleep queue  
  Thread B cannot pull that job in sleep queue because sleep2ready isn't executed until the A's long job pass control to scheduler

---

## Comment 3

> Username: olk  
> Created at: 2016-11-27 11:52:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-263117884  

- remote-ready-queue is not useless - how would you notify the scheduler of thread A that its fiber f1 is ready (was waiting on condition_variable) from thread B (signals condition_variable).  
- sleep-queue should only be modified by its owning scheduler - otherwise it would require synchronization

---

## Comment 4

> Username: ishitatsuyuki  
> Created at: 2016-11-27 11:59:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-263118188  

- remote-ready-queue isn't needed for threaded applications using things like shared_work; they do the synchronization itself on ready-queue. It doesn't necessarily need a dedicated queue.  
- allow the algorithm to synchronize itself; locking isn't so expensive since atomic based queues exist, we need the maximum flexibility on work-stealing instead

---

## Comment 5

> Username: ishitatsuyuki  
> Created at: 2016-12-05 09:40:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-264807899  

The concept:  
- Attached attribute 'timer' for an asio timer, used for scheduling sleeping (asio manages sleep queue on itself)  
- Shared ready queue  
- Instead of set_ready, use a special mutex-unlocked version in the asio callbacks  
  
The pseudocode:  
```  
void set_ready(ctx) // Previously awakened  
{  
    lock_guard lk;  
    que.push(ctx);  
}  
void set_ready_unlocked(ctx) // omitted  
void set_remote_ready(ctx)  
{  
    set_ready(ctx);  
    // It's useful for non work-sharing schedulers  
}  
void set_sleep(ctx)  
{  
    ctx.attr['timer'].async_wait([&]{set_ready_unlocked(ctx);});  
}  
ctx* pick_next() // A different design, block until jobs are available  
{  
    lock_guard lk;  
    while(que.empty()) {iosvc.run_one();iosvc.poll();}  
    return que.first();  
}  
// suspend and notify are removed in favor of internal sleep impl  
```

---

## Comment 6

> Username: nvachhar  
> Created at: 2018-01-10 22:04:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-356752310  

Any thoughts on the proposal here? I'm also interested in avoiding long-running work on one thread preventing a fiber from being scheduled on another. I think deferring to the scheduling algorithm, but having default implementations to keep existing algorithms still correct ought to be possible?

---

## Comment 7

> Username: olk  
> Created at: 2018-01-11 06:47:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-356842046  

I'm sorry - I'm too busy ...

---

## Comment 8

> Username: klmurphy72  
> Created at: 2018-09-12 20:19:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-420783392  

Has any more thought been put into this?  I tend to agree that there needs to be a way to move items in the remote_ready queue to a scheduler that that can actually schedule it instead of waiting for the original scheduler to "wake up".  
  
If some of the logic was moved to algorithm away from the scheduler the queues for sleep and remote_queue could be kept in a way that ready fibers could be scheduled without waiting for the original scheduler.  
  
I don't think that "synchronization would be required" is a reason not to implement.  
  
Would some change in this area help with ASIO integration?  The idea being that we need to get a way from fibers being so tied to schedulers.  If it did not matter which scheduler processed it, we would not be hamstrung by the fact that you cannot wake a particular ASIO thread if multiple threads are executing "run()".  
  
Just my $.02, thoughts?

---

## Comment 9

> Username: olk  
> Created at: 2018-09-19 11:15:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/89#issuecomment-422761899  

unfortunately not yet -at the moment I've too many other stuff on my desk
