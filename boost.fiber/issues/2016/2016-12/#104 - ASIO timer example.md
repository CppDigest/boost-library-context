# #104 - ASIO timer example [Closed]

> Username: kirillv  
> Created at: 2016-12-13 08:38:57 UTC  
> Updated at: 2017-02-17 06:59:17 UTC  
> Closed at: 2017-02-17 06:59:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/104  

Hi. Thanks for this library! What is the best practice for using timer callbacks (asio + fibers)? I can see simple tcp example (echo), and the code is clear. But it would be great if example with asio timers would appear (or piece of code).  
Thanks in advance!

---

## Comment 1

> Username: olk  
> Created at: 2017-02-17 06:59:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/104#issuecomment-280570525  

You could use the same pattern as boost.asio demonstrates using coroutines (e.g. asio's spawn and yield).
