# #87 - Examples are not complete (and or missing docs) [Closed]

> Username: heretic13  
> Created at: 2018-02-17 08:40:36 UTC  
> Updated at: 2020-12-30 00:49:15 UTC  
> Closed at: 2020-12-30 00:49:09 UTC  
> Url: https://github.com/boostorg/asio/issues/87  

The examples for asio do not contain the cleanup code and the correct shutdown of the application. You can report to me that this is beyond the scope of demonstrating the possibilities of asio, but you will be wrong for three reasons:  
  
**1**. The program (even the one that runs on the server) needs to be stopped correctly. After all, you are stopping the Apache process, the SQL server and so on and do not kill it. At least because the program must perform some actions before it is unloaded.  
  
**2**. You can not argue that your library (engine) is working correctly in all cases if you did not check it under stopping. You can not convince the Big Boss that this works, because the Big Boss may ask you to stop the program (not to kill the process) during the demonstration. And what do you say? I can not, because the library (or example) does not allow it? How will you look in the eyes of the Big Boss?  
You provided examples of "echo-server". Have you checked how it works with 10,000 connections and the user makes a stop program? If you append some lines of code in your examples - users will do these checks for you. Possible errors identified will be written in the bug reports, after correcting them the library will become more stable.  
  
**3**. If I understand correctly, the basic idea of ​​asio is to use **1 io_service object and a number of processing threads** to the entire application. This implicitly involves the use of **std :: enable_shared_from_this**. Programs written with this technology are more difficult to correctly stop than ordinary programs. And of course in your examples this is not done :). I did not find any mention of this problem in the documentation. But the problem exists, Google says that I'm not the only one who worries about this question - how correctly to stop the boost / asio program that uses the model "**1 io_service + several threads + many objects inherited from std :: enable_shared_from_this**"? There are very few good and correct answers. You can report to me that each program is unique - you can not describe all the cases in the documentation. Yes it is. But boost was developed to ensure that programmers around the world did not "invent bicycles", but used ready-made foreign developments. You could provide the page in the documentation with the basics - how to write the correct stopping, or do it in a particular case - in the examples. This would be a good starting point for other programmers that use boost, would greatly save time on "inventing bicycles".  
That's all I wanted to say.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:48:59 UTC  
> Url: https://github.com/boostorg/asio/issues/87#issuecomment-752289056  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#600](https://github.com/chriskohlhoff/asio/issues/600).
