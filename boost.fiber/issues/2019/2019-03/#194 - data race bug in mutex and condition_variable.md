# #194 - data race bug in mutex and condition_variable [Closed]

> Username: romange  
> Created at: 2019-03-03 23:30:04 UTC  
> Updated at: 2019-03-11 07:34:37 UTC  
> Closed at: 2019-03-11 07:34:37 UTC  
> Url: https://github.com/boostorg/fiber/issues/194  

Hi Oliver,  
I suspect that scenario below uncovers possible bug in condition_variable and would like to consult with you about it..  
  
Suppose thread A waits for some condition that comes from thread B:  
```  
{  
  ::boost::fibers::condition_variable cnd;  
  fibers::mutex mu;  
  std::thread([&] {    // Thread B  
  mu.lock();  
  signal = true;  
  mu.unlock();  
  cnd.notify_one(); }).detach();  
  
 // Thread A.  
  std::unique_lock<fibers::mutex> lock(mu_);  
  cnd.wait(lock, ...);  
}  // cnd goes out of scope ****  
```  
inside `condition_variable::notify_one()` you have:  
```  
...  
detail::spinlock_lock lk{ wait_queue_splk_ };  
 while ( ! wait_queue_.empty() ) {  
   ....  
   active_ctx->schedule( ctx);         
 }  
```  
Imagine that `notify_one()` switches the execution from thread B back to thread A right after the `schedule` call but before `lk` went out of scope.  `cnd` becomes unblocked, it goes out of scope first. At some point the execution resumes in thread B, it calls the destructor of `lk` which unlockes already disposed variable `wait_queue_splk_`. Could it happen?

---

## Comment 1

> Username: olk  
> Created at: 2019-03-04 07:23:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469145287  

`cnd` is allocated on the stack of thread A  
thread B holds a _reference_ to `cnd`  
if the block in thread A goes out of scope - variable `cnd` is deallocated  
it is a bug in your example to hold a reference of `cnd` in the detached thread B beyond the lifetime of thread A (the code block)  
this problem is not specific to boost.fiber - it applies to all concurrent ToEs

---

## Comment 2

> Username: romange  
> Created at: 2019-03-04 07:36:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469148312  

@olk  Wait a second. it's a bug because `cnd.notify()` continues accessing `this`  *AFTER* thread A resumed it's operation after the `wait` call. For all condition variables I know, it's safe calling `notify()` !

---

## Comment 3

> Username: romange  
> Created at: 2019-03-04 07:38:47 UTC  
> Updated at: 2019-03-04 07:40:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469148806  

@olk  it's not a bug to hold a reference, it's a bug to access it. and I do not access it. `cnd.notify()` release control before it should. In fact, the same logic appears in more places in fibers codebase:  
  
https://github.com/boostorg/fiber/blob/master/include/boost/fiber/unbuffered_channel.hpp#L125

---

## Comment 4

> Username: olk  
> Created at: 2019-03-04 08:34:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469163361  

>  it's not a bug to hold a reference, it's a bug to access it  
  
What I mean is, the lifetime of `cnd` is not synchronized between threads A and B.  
A owns `cnd` because it is allocated on A stack (code block).  
Because B is a **_detached_** thread it can access `cnd` after A has left the code block/destroyed `cnd`.  
The code must take care that `cnd` lives as long as thread B tries to use it.

---

## Comment 5

> Username: romange  
> Created at: 2019-03-04 08:55:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469169785  

@olk  I totally agree and I am sorry I gave you incomplete example. Can you please see the example below?   
  
1. Does it look to you like a valid user code?   
2. Do you expect it to data race with std primitives?  
3. Do you expect it to data race with fibers primitives?  
  
```  
struct Foo {  
 condition_variable cnd;  
  mutex mu;  
  bool signal_ = false;  
    
  void signal() {  
    mu.lock();  
    signal_ = true;  
    mu.unlock();  
   cnd.notify_one();   
  }    
};  
  
int main() {  
 Foo* foo = new Foo;  
  std::thread([foo] {    // Thread B  
     foo->signal();  
   }).detach();  
  
 // Thread A.  
  std::unique_lock<fibers::mutex> lock(foo->mu);  
  foo->cnd.wait(lock, [foo] { return foo->signal_;} );  
  delete foo;  
  
  return 0;  
}  
```  
  
My claim: it's a valid code. it won't data race with std mutex and condition_variables. It data races with fibers.  And the fix is very easy in case of condition_variable. All we need is to add `lk.unlock();` before calling `active_ctx->schedule( ctx);`  here (2 places of course):  
https://github.com/boostorg/fiber/blob/master/src/condition_variable.cpp#L29

---

## Comment 6

> Username: olk  
> Created at: 2019-03-04 13:14:50 UTC  
> Updated at: 2019-03-04 13:15:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469248770  

OK, I got your point - a solution could be to defer scheduling signaled fibers in notify() till the lock was released.

---

## Comment 7

> Username: romange  
> Created at: 2019-03-04 13:46:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469258095  

Thanks! I've fixed it for now in my codebase (as workaround till fixed boost lib will be released) like this:  
https://github.com/romange/gaia/blob/master/util/fibers_ext.cc with unlocking the spinlock before signaling fibers. I think it's correct because you use spinlock to protect waiters queue.  
  
In case of `notify_all();` I think it's correct to assume that as long as there are waiters you do not need to unlock spinlock.

---

## Comment 8

> Username: romange  
> Created at: 2019-03-05 19:36:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469827773  

Actually I also had a bug in `notify_all()`  : `!wait_queue_.empty()` check was unprotected during the last iteration. Eventually, I decided just to store all ctx candidates into a temporary intrusive list and then activate them after I unlock the lock: https://github.com/romange/gaia/blob/master/util/fibers/fibers_ext.cc#L69  
  
@olk  would you like me to send you the fix or you prefer fixing it yourself?

---

## Comment 9

> Username: olk  
> Created at: 2019-03-06 06:41:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-469988542  

Using a seperate list was what came to my mind too - patches are welcome.

---

## Comment 10

> Username: romange  
> Created at: 2019-03-08 12:21:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-470910524  

@olk  The same bug exists also in `mutex::unlock()`:  
https://github.com/boostorg/fiber/blob/develop/src/mutex.cpp#L75

---

## Comment 11

> Username: romange  
> Created at: 2019-03-11 07:14:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-471428676  

@olk  After thinking about it more, I realized that my example code has a bug: if thread B starts first and preempts right after `mu.unlock();` line but before `cnd_.notify()` was called, then thread A will pass   
  
```  
std::unique_lock<fibers::mutex> lock(foo->mu);  
foo->cnd.wait(lock, [foo] { return foo->signal_;} );  
```  
lines without blocking and then delete `foo`. Thread B will access the released condition variable.   
The only solution for such pattern is to move `cnd_.notify()` under the lock.   
  
I still think that it's better to minimize spinlock contention and unlock it earlier but it's not required for correctness. Should I close this issue and the PR I've sent?

---

## Comment 12

> Username: olk  
> Created at: 2019-03-11 07:29:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/194#issuecomment-471431937  

yes, please - ty
