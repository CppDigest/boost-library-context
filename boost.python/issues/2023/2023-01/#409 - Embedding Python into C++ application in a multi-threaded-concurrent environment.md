# #409 - Embedding Python into C++ application in a multi-threaded/concurrent environment? [Open]

> Username: luz-arreola  
> Created at: 2023-01-29 16:13:54 UTC  
> Updated at: 2023-01-30 01:42:13 UTC  
> Url: https://github.com/boostorg/python/issues/409  

I am able to use **Boost.Process** to successfully execute a Python script from within a C++ application.  The C++ app can obtain the data returned by Python and this is very trivial.  
  
The problem is performance.  Creating a new process needed to execute the Python script is very expensive, e.g. 60ms on a mid-level desktop Windows PC.  I have not tested on Linux yet but I'm sure that there will still be a performance hit for each process.  
  
Things that have come to mind:  
  
- fastcgi for python  
- execute python scripts concurrently in different threads (obviously without creating a new process for each script as I do at this time)  
  
Can **Boost.Python** help me with either of these ideas?  I've seen older posts online and people seem to give up with the concurrency method.  Please advise, thank you.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2023-01-29 17:43:16 UTC  
> Url: https://github.com/boostorg/python/issues/409#issuecomment-1407726045  

Yes ! Embedding Python into C++ doesn't mean to run a subprocess. Instead, you can run a Python interpreter within the same process and share your application state with the Python runtime.  
That is described in http://boostorg.github.io/python/doc/html/tutorial/tutorial/embedding.html  
  
Concurrency is still an issue, but mostly only due to the GIL (https://wiki.python.org/moin/GlobalInterpreterLock), i.e. the problem is due to Python's internal design and entirely independent of Boost.Python.

---

## Comment 2

> Username: luz-arreola  
> Created at: 2023-01-30 00:51:00 UTC  
> Updated at: 2023-01-30 00:53:41 UTC  
> Url: https://github.com/boostorg/python/issues/409#issuecomment-1407830927  

In the past, I have used fastcgi on both IIS and Apache web servers to avoid the expense of creating a process for each request.  What happens is that fastcgi creates a pool and any process that is not busy is automatically used.  This works great for php applications as well as C++ executables.  
  
Of course, what I was thinking of doing would be very different, e.g. my app would used an unused python process from the pool thus eliminating all of the process construction/deconstruction.  Of course, I have no idea how I would go about communicating with the pool of python processes from my C++ application because the documentation I remember was very sparse and not too helpful.  I will look a little into your link on GIL, but this does seem a bit frightening to be quite honest.    
  
Thank you for your time and I will eagerly read any suggestion and or comment that you may have.  Best regards!

---

## Comment 3

> Username: luz-arreola  
> Created at: 2023-01-30 01:42:13 UTC  
> Url: https://github.com/boostorg/python/issues/409#issuecomment-1407862672  

I will be looking at this: https://fastcgi-archives.github.io/FastCGI_Developers_Kit_FastCGI.html  
  
Like I said, I have used fastcgi on c++ cgi executables as well as php websites.  It makes a huuuuge difference because each request is handled by an available (reusable) process.  Like I said, on my PC, it takes 60ms to create a new process to handle a request.
