# #454 - Trying to use objects that have multithreading crashes the jupyter kernel [Closed]

> Username: aromanro  
> Created at: 2024-09-26 15:26:07 UTC  
> Updated at: 2024-09-28 10:23:29 UTC  
> Closed at: 2024-09-28 09:54:01 UTC  
> Url: https://github.com/boostorg/python/issues/454  

I implemented a python library using boost::python. I test it usually in Windows environment (although it can be compiled on linux and mac, too, I didn't test yet if there the behaviour is the same).  
  
Everything works as it should, except when I attempt to execute something multithreaded using either async/future or std::thread.  
An attempt to do so crashes the kernel.  
  
At first I noticed that with async/future (even trying to execute an empty lambda with a void return crashes at the attempt of the future.get())... then I switched to my own implementation of a thread pool that uses std::thread, where it crashes as well.  
  
Is this a known problem? If yes, is there a workaround?  
  
Open MP multithreading works fine, by the way, but I need more flexibility than that.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2024-09-26 15:38:41 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2377318855  

Using Python in multi-threaded environments requires you to acquire / release the GIL ("Global Interpreter Lock") properly. Calling from Python into C++ should release the GIL for any non-trivial C++ functions, while C++ code that's calling back into Python should (re-)acquire the GIL before passing control to the Python runtime.

---

## Comment 2

> Username: aromanro  
> Created at: 2024-09-26 15:49:42 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2377344139  

I don't use python from the library, so there is no c++ calling into python.  
  
I also don't use GIL, the library uses internally its own computing threads that have their own locking mechanism. There is no multithreading as 'seen' from the python calls, despite the existing threads.  
  
The python calls into the library go into a single thread.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2024-09-26 15:55:09 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2377355791  

I agree in that case you don't need to deal with the GIL. But what makes you think that the problem is related to Python or Boost.Python, rather than some C++-internal multi-threading bug ? (I'm not a regular Windows user, but I do remember having to carefully select a runtime library to match my multi-threading choices...)

---

## Comment 4

> Username: aromanro  
> Created at: 2024-09-26 15:58:19 UTC  
> Updated at: 2024-09-26 16:01:56 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2377362732  

I have tests for the c++ part, which pass. The library is not used only in python.  
  
I changed the code in there to have simply an empty lambda that returns nothing, launched with async, just a simple get call on the future exhibits the problem.  
  
After I switched to a quite different method - my own threads, avoiding async/future, the same behaviour persists.  
  
I'll check the compiling/linking related stuff for the dll, maybe it's something in there...

---

## Comment 5

> Username: aromanro  
> Created at: 2024-09-26 16:28:31 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2377426841  

As an additional info, the variant with thread pooling does the crash without having the threads execute anything: they are just created and started at that point and they 'run' idle, just waiting for jobs.

---

## Comment 6

> Username: aromanro  
> Created at: 2024-09-27 09:59:06 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2378907538  

Even more info on this: it's not thread creation or running alone... it's using a mutex in them that causes this.  
Unfortunately I need it to for having waiting for jobs and signaling when to terminate the threads.  
  
I might have some workarounds, but they are very limiting.

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2024-09-27 11:38:58 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2379075811  

I have plenty of multi-threaded code using Boost.Python. I strongly doubt your problem is related to Boost.Python.

---

## Comment 8

> Username: aromanro  
> Created at: 2024-09-27 11:53:18 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2379098902  

Me too. It was with open mp, though.  
  
I started doing the changes to avoid any thread creation/stopping/releasing in constructors/destructors, this seems to help.

---

## Comment 9

> Username: aromanro  
> Created at: 2024-09-28 09:53:56 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2380586759  

Turns out it's related with release compiling optimizations. Maybe a VC++ bug or something.  
I compiled the dll in debug mode and it works fine this way.  
  
Still trying to find the cause, but it's obviously unrelated with boost::python so I'm closing this.

---

## Comment 10

> Username: aromanro  
> Created at: 2024-09-28 10:18:15 UTC  
> Updated at: 2024-09-28 10:23:29 UTC  
> Url: https://github.com/boostorg/python/issues/454#issuecomment-2380593246  

I'll leave this here just in case somebody has a similar issue.  
  
  
The problem was that the attempt to lock a mutex did not succeed for any of the threads (even if I launched a single one). I needed that for having a wait on a condition that signaled that work is available, and obviously for having access to data protected between threads.  
  
The bug manifested itself not only with my thread pool (again, trying to lock a single mutex and removing everything else to be sure it's not something else, failed to acquire the mutex for any of the threads, even if it was only one thread involved), but also with the async/future implementation (even where no mutex was used, just simple lambdas that returned results or even nothing, the get() on the future did the crash).  
  
  
The final resolution was to build the dll with 'multi-threaded' instead of 'multi-threaded dll'.
