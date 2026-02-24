# #1084 - Is the examples provided really async? [Closed]

> Username: runesig  
> Created at: 2018-04-01 09:11:31 UTC  
> Updated at: 2018-05-17 16:58:10 UTC  
> Closed at: 2018-05-17 16:58:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1084  

I am trying out the async http client example that is porvided with the source. I have made a very simple Windows application to test it. The thing is that it freezes the application when posting. I am using the examples as is, but instead of using a console application it is run under win32.  
  
    // Launch the asynchronous operation  
    std::make_shared<session>(ioc, ctx)->run(host, port, target, version);   
  
I was not expecting the GUI to freeze when by calling this code?

---

## Comment 1

> Username: carolinebeltran  
> Created at: 2018-04-01 16:56:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-377800341  

Something is blocking what I assume to be the main thread, and I would assume that it's a sync operation.  Which Beast example are you referring to?  
  
From your snippet, I cannot see if ioc is running on the same thread as your GUI.  My guess would be that you are running your io_context on the same (single) thread--if a sync call is made, your GUI would definitely freeze.  
  
My first suggestion would be to never run a GUI and io_context on the same thread.  Secondly, make sure that the i/o operations begin with **async**, e.g. async_read() vs read().  Having your GUI on its own thread will ensure that your GUI is never blocked.

---

## Comment 2

> Username: runesig  
> Created at: 2018-04-01 17:30:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-377802694  

OK, I thought there were some "magic" behind the scenes that created a new thread this since the example is called "async". This is the example: https://github.com/boostorg/beast/tree/develop/example/http/client/async

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-01 18:03:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-377805058  

Beast assumes that the user is already familiar with Asio's asynchronous model, see:  
https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/introduction.html#beast.introduction.requirements

---

## Comment 4

> Username: runesig  
> Created at: 2018-04-01 18:45:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-377807790  

Thanks, I will study that.

---

## Comment 5

> Username: TechnikEmpire  
> Created at: 2018-04-01 20:52:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-377815874  

@runesig just look at some simple ASIO samples. You need a IO service with N threads running against it and a work object to prevent the reactor from exiting when it has no work. When you invoke async functions they get delegated to the IO service to poll and invoke handlers on one or more threads. That's where the async "magic" comes in.

---

## Comment 6

> Username: runesig  
> Created at: 2018-04-01 21:21:11 UTC  
> Updated at: 2018-04-01 21:21:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-377817724  

Thank you very much @TechnikEmpire  !

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-04-10 15:58:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-380153863  

Has this issue been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-05-10 16:42:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-388111284  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created at: 2018-05-17 16:58:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1084#issuecomment-389937000  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
