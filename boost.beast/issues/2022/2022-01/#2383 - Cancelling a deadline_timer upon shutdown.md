# #2383 - Cancelling a deadline_timer upon shutdown [Closed]

> Username: flashnuke  
> Created at: 2022-01-24 13:47:12 UTC  
> Updated at: 2022-01-24 17:19:30 UTC  
> Closed at: 2022-01-24 16:04:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2383  

I'm trying to invoke a shutdown, and if I have an active timer it doesn't close properly.  
Other than stopping the io service, and calling `async_close`, I have a pending timer that I'm trying to stop by calling `timer.cancel()`.  
  
For some reason it doesn't work, and after reading the documentation I stumbled upon this:   
  
"This function (`timer.cancel()`) forces the completion of any pending asynchronous wait operations against the timer. The handler for each cancelled operation will be invoked with the boost::asio::error::operation_aborted error code.  
Cancelling the timer does not change the expiry time."  
  
What is meant by "forces the completion"? Are pending operations forced immediately or is the timer still active?  
  
Is there any way to halt the timer and cancel the pending operations immediately?

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-01-24 13:56:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2383#issuecomment-1020126712  

The completion handler for any asio object's async operation is submitted to the io_context for invocation. The handler will be called from the same thread that is calling `io_context::run()`.  
  
In real terms that means that the completion handler will be executed some time after the call to `timer.cancel()`.  
  
> Is there any way to halt the timer and cancel the pending operations immediately?  
  
No, it is an invariant guarantee that the completion handler submitted as part of an asynchronous operation will always execute and will always be executed by the associated io_context, one one of the threads that is calling `io_context.run()` or `io_context::poll()`.  
  
They way the code detects that it was cancelled is by interrogating the error code passed to the completion handler.  
  
It has to be this way, because imagine you have the following sequence:  
  
1. start an `async_wait` on a timer  
2. the timeout completes and submits the completion handler to the io_context for invocation with no error  
3. immediately some other async operation calls `timer.cancel()`. This cancel is ignored because there are currently no asynchronous waits in progress.  
4. time passes  
5. the completion handler from the `async_wait` is invoked.  
At this point, your signals and events are crossed. The only way reliably to tell that the wait either completed or was cancelled  
is to interrogate the error code. This applied to all asynchronous programs and all cross-thread cancellation mechanisms.

---

## Comment 2

> Username: flashnuke  
> Created at: 2022-01-24 14:59:48 UTC  
> Updated at: 2022-01-24 15:00:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2383#issuecomment-1020189631  

thanks @madmongo1!  
  
I let all tasks finish gracefully... anyhow, I still get an error when trying to close the socket:  
```  
mCurrentActiveWS->async_close(  
                    beast::websocket::close_code::normal,  
                    [&] ( const beast::error_code& ec) {  
                       std::cout << ec.message();   
});  
```  
I get the following message: `Operation canceled` which is error code 89...  
  
what am I doing wrong? (I don't think it's related to the timer now)

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-01-24 15:21:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2383#issuecomment-1020213943  

Getting operation cancelled from this async_close is expected if the connection has already detected an error or the underlying transport is already closed.  
  
I'd have to have a sense of the message flow in order to comment accurately.  
  
Are you able to create a 1-page program that exhibits the issue so I can compile and run it locally?  
  
Easiest to share it on github.  
  
Don't share your entire project - just enough code to reliably demonstrate the problem.

---

## Comment 4

> Username: flashnuke  
> Created at: 2022-01-24 16:04:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2383#issuecomment-1020259331  

I was accidentally trying to close the same connection twice, it works perfectly now...  
thanks!

---

## Comment 5

> Username: madmongo1  
> Created at: 2022-01-24 17:19:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2383#issuecomment-1020345753  

> I was accidentally trying to close the same connection twice, it works perfectly now... thanks!  
  
It's amazing how often building a little test program, or even just going through the mental exercise of doing so, fixes problems like this. Works every time.  
  
Glad it's resolved.
