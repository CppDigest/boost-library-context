# #2277 - ws_async_write :: this->queue_._Mypair._Myval2._Myfirst->_Ptr was nullptr (Access Violation Error) [Closed]

> Username: vtharmalingam  
> Created at: 2021-07-05 10:23:44 UTC  
> Updated at: 2021-07-05 18:25:39 UTC  
> Closed at: 2021-07-05 18:25:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2277  

Hi Vinnie,  
  
Greetings! I use your famous WS example code, and this is one of the functions in it.  
  
Seldom (inconsistently) this function throws access violation error. I am not great at Buffer internals and they seem to be using the "Pair" within. Even if I do the queue to be non-empty as a pre-requisite for the recursive call, I do not get my head around the access-violation error. can you please throw lights on it? Thanks, in advance.  
  
Thanks,  
Tharma  
  
```cpp  
  
void  
websocket_session::  
on_write(error_code ec, std::size_t)  
{  
    // Handle the error, if any  
    if(ec)  
       return fail(ec, "write");  
  
    // Remove the string from the queue  
    queue_.erase(queue_.begin());  
  
    // Send the next message if any  
    if(! queue_.empty())  
        ws_.async_write(				// ==> this->queue_._Mypair._Myval2._Myfirst->_Ptr was nullptr (Access Violation Error)  
            net::buffer(*queue_.front()),  
            [sp = shared_from_this()](  
                error_code ec, std::size_t bytes)  
            {  
                sp->on_write(ec, bytes);  
            });  
}  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-05 14:47:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2277#issuecomment-874167961  

> You probably have a race condition. Is your program multi-threaded? Did you forget that you must not initiate a second write before the completion handler of the first has been executed?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-07-05 15:12:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2277#issuecomment-874184430  

Does the unmodified example program exhibit the access violation?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-07-05 15:13:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2277#issuecomment-874185336  

FYI, this error:  
  
```  
this->queue_._Mypair._Myval2._Myfirst->_Ptr was nullptr (Access Violation Error)  
```  
  
happens when `front()` is called on an empty container. This means that there is a race condition which allows the queue to be modified after the call to `empty()` and before the call to `front()` above.

---

## Comment 4

> Username: vtharmalingam  
> Created at: 2021-07-05 17:24:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2277#issuecomment-874249561  

> Does the unmodified example program exhibit the access violation?  
No, modified one. I call session::send from multiple threads. I understand that is what causing the issue. Thanks for educating me on that. Really appreciated!

---

## Comment 5

> Username: vtharmalingam  
> Created at: 2021-07-05 17:34:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2277#issuecomment-874252892  

BTW, is vector a good container for a multi-threaded environment, Or boost::beast buffers say, flat_buffer kind?  I am not really good at buffers today, but if you advise they are appropriate, then I can work in those directions.   
  
Thank you very much @madmongo1 and @vinniefalco  for your quick help!  
@vinniefalco  I was so inspired of your great work a few years back on the beast, so you know :)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-07-05 17:55:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2277#issuecomment-874260135  

The container is not the part that needs to be made thread-safe, but rather your calling code. In this case you need to modify `send` so that it is thread-safe. If you area proficient with writing multi-threaded programs this should not be a problem. However, it sounds like you are still learning. Thus my advice is to make your program **single-threaded**, and neatly avoid all the hassles that come with multiple threads.

---

## Comment 7

> Username: vtharmalingam  
> Created at: 2021-07-05 18:11:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2277#issuecomment-874266865  

Got you, sure will proceed as you suggested. Thank you, once again!

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-07-05 18:25:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2277#issuecomment-874272240  

I’ll go ahead and close this issue. If you get stuck you’re welcome to open a discussion on the GitHub page. Happy to respond .
