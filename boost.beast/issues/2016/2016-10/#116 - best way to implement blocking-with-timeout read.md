# #116 - best way to implement blocking-with-timeout read [Closed]

> Username: ghost  
> Created at: 2016-10-08 23:40:30 UTC  
> Updated at: 2016-10-19 11:07:13 UTC  
> Closed at: 2016-10-19 11:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/116  

Hi Vinnie,  
  
I am wondering - what is the best way to implement a blocking websocket read (say) that returns either after something is received or a given timeout period elapses? Is this something that should go into Beast, or something that should be written on top of it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-09 10:17:33 UTC  
> Url: https://github.com/boostorg/beast/issues/116#issuecomment-252477939  

For asynchronous operations, there are many ways to implement a timer - each with its own advantages and disadvantages. For this reason, it makes more sense to push the responsibility for providing timeout facilities on to users of Beast. Asio makes a similar choice - none of its interfaces allow for timeouts, but you can build a timeout facility yourself.  
  
For synchronous operations, I don't know that its possible to implement a timeout feature. How would it be done? `ip::tcp::socket::cancel` is not thread safe.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-10-14 21:51:09 UTC  
> Url: https://github.com/boostorg/beast/issues/116#issuecomment-253928379  

Is this resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-19 11:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/116#issuecomment-254782156  

Looks like this is no longer an issue. If you feel it is still relevant, please re-open it.
