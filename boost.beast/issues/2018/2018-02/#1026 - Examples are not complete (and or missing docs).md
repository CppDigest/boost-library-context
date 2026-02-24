# #1026 - Examples are not complete (and or missing docs) [Closed]

> Username: heretic13  
> Created at: 2018-02-17 08:45:42 UTC  
> Updated at: 2018-03-31 22:54:27 UTC  
> Closed at: 2018-02-22 00:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1026  

This is a common problem of boost/asio and boost/beast.  
  
The examples for beast do not contain the cleanup code and the correct shutdown of the application. You can report to me that this is beyond the scope of demonstrating the possibilities of beast, but you will be wrong for three reasons:  
  
**1**. The program (even the one that runs on the server) needs to be stopped correctly. After all, you are stopping the Apache process, the SQL server and so on and do not kill it. At least because the program must perform some actions before it is unloaded.  
  
**2**. You can not argue that your library (engine) is working correctly in all cases if you did not check it under stopping. You can not convince the Big Boss that this works, because the Big Boss may ask you to stop the program (not to kill the process) during the demonstration. And what do you say? I can not, because the library (or example) does not allow it? How will you look in the eyes of the Big Boss?  
You provided examples of "http-server". Have you checked how it works with 10,000 connections and the user makes a stop program? If you append some lines of code in your examples - users will do these checks for you. Possible errors identified will be written in the bug reports, after correcting them the library will become more stable.  
  
**3**. If I understand correctly, the basic idea of ​​asio is to use **1 io_service object and a number of processing threads** to the entire application. This implicitly involves the use of **std :: enable_shared_from_this**. Programs written with this technology are more difficult to correctly stop than ordinary programs. And of course in your examples this is not done :). I did not find any mention of this problem in the documentation. But the problem exists, Google says that I'm not the only one who worries about this question - how correctly to stop the boost / asio program that uses the model "**1 io_service + several threads + many objects inherited from std :: enable_shared_from_this**"? There are very few good and correct answers. You can report to me that each program is unique - you can not describe all the cases in the documentation. Yes it is. But boost was developed to ensure that programmers around the world did not "invent bicycles", but used ready-made foreign developments. You could provide the page in the documentation with the basics - how to write the correct stopping, or do it in a particular case - in the examples. This would be a good starting point for other programmers that use boost, would greatly save time on "inventing bicycles".  
That's all I wanted to say.

---

## Comment 1

> Username: heretic13  
> Created at: 2018-02-17 08:46:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1026#issuecomment-366427126  

same for asio - https://github.com/boostorg/asio/issues/87

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-02-17 14:01:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1026#issuecomment-366443376  

I am in 100% agreement with you! I used to joke about it at my last job, I spent over a month adding the "exit" feature (LOL). Doing a super-clean exit of an HTTP server, especially multi-threaded, is a decidedly non-trivial endeavor. It would greatly complicate the examples. I'll mull this over and see if I can come up with something.  
  
That said, management of connections is outside the scope of the library.

---

## Comment 3

> Username: carolinebeltran  
> Created at: 2018-03-31 19:19:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1026#issuecomment-377716676  

I've briefly thought about this before due to concern that I may kill the process while interrupting some NO-SQL database operation for example.  NO-SQL databases do not have transaction/rollback support and killing the Beast server could cause data to be left in an incomplete state.  
  
My thoughts were to set a 'global' value to true which would cause the **handlers** for **async_wait** operations ignore new incoming requests.  Then start a timer that shutsdown the Beast server upon expiration.  This should give non-completed tasks time to finish.  I have not tried this idea since I am using an SQL server but I will be using a NO-SQL server soon (without transaction/rollback support).  
  
Does **blocking incoming requests** and **using a timer to shutdown** seem feasible?

---

## Comment 4

> Username: djarek  
> Created at: 2018-03-31 20:58:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1026#issuecomment-377722967  

@carolinebeltran   
You don't even need a timer - just prevent new requests, handle all pending operations, and close all open IO objects. Threads running in the `io_context` should leave `run()` once there's no work pending.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-03-31 22:54:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1026#issuecomment-377729155  

@carolinebeltran The advanced servers now include code to perform a clean exit, see: https://github.com/boostorg/beast/commit/10ce0283c2362eb3cc156f59cacb5c5c50ddb8ee
