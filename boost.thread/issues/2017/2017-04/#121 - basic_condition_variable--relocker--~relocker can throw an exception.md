# #121 - basic_condition_variable::relocker::~relocker can throw an exception [Closed]

> Username: kalman5  
> Created at: 2017-04-12 12:30:59 UTC  
> Updated at: 2017-08-23 06:58:20 UTC  
> Closed at: 2017-08-23 06:06:47 UTC  
> Url: https://github.com/boostorg/thread/issues/121  

relocker DTOR is implemented as the following:  
  
                ~relocker()  
                {  
                    if(unlocked)  
                    {  
                        lock.lock();  
                    }  
  
                }  
  
that lock.lock() can throw (see unique_lock::lock in lock_types.hpp) leading to std::terminate if compiled in c++11 mode

---

## Comment 1

> Username: viboes  
> Created at: 2017-04-13 06:35:29 UTC  
> Url: https://github.com/boostorg/thread/issues/121#issuecomment-293799952  

I will try to do the lock outside a destructor and propagate the exception.  
  
Please could you tell me when do you see the call to terminate?

---

## Comment 2

> Username: viboes  
> Created at: 2017-04-13 16:44:27 UTC  
> Url: https://github.com/boostorg/thread/issues/121#issuecomment-293956633  

The problem I have are the exception warranties.  
What will be the state when there is a failure when trying to lock?  
The resource will be unusable.   
What the user should do when receiving an exception?  
  
  
There are other uses similar to the relocker elsewhere in the library.

---

## Comment 3

> Username: kalman5  
> Created at: 2017-04-13 19:11:38 UTC  
> Updated at: 2017-04-14 11:54:11 UTC  
> Url: https://github.com/boostorg/thread/issues/121#issuecomment-293994989  

You need to mark those DTOR as noexcept(false); if they can throw.  
Basically even while not being inside a stack unwinding that will make the software terminating.

---

## Comment 4

> Username: viboes  
> Created at: 2017-04-14 22:08:44 UTC  
> Url: https://github.com/boostorg/thread/issues/121#issuecomment-294246008  

Ok, I see.

---

## Comment 5

> Username: viboes  
> Created at: 2017-08-23 06:06:47 UTC  
> Url: https://github.com/boostorg/thread/issues/121#issuecomment-324230602  

Done in boost 1.65.
