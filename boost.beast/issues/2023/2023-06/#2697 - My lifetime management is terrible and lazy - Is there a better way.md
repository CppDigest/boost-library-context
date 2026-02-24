# #2697 - My lifetime management is terrible and lazy - Is there a better way? [Closed]

> Username: systocrat  
> Created at: 2023-06-20 12:19:12 UTC  
> Updated at: 2023-06-22 16:03:13 UTC  
> Closed at: 2023-06-22 16:03:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2697  

Hey everyone,  
  
I have some code that I wrote in the interest of expediency, but I'd like to clean it up and learn in the process.  
  
```  
        void stop() {  
            if (ws_.is_open()) {  
                ws_.async_close(websocket::close_code::normal, [self=shared_from_this()](boost::system::error_code ec) {  
                    self->msg_channel_.cancel();  
                    self->buffer_.clear();  
                    self->resolver_.cancel();  
  
                    self->msg_queue_ = {};  
                });  
            }  
  
            stopped_ = true;  
        }  
```  
  
As you can see, in a stop() function that closes my Websocket session, I call async_close witth a lambda that captures the owning classes shared_ptr and does all of its cleanup thereafter.  
  
I'm not the biggest fan of using shared_ptr so frivolously in the first place, and I'm especially not a fan of capturing a shared_ptr in a lambda.  
  
Is there a better way? Should I make the stop function an async operation? What are best practices when thinking about this kind of thing?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-20 14:54:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2697#issuecomment-1598954587  

That really depends on your project and it's code-style. I like to use coroutines to make this stuff async, but your solution seems fine as well. Really hard to say without more info.

---

## Comment 2

> Username: systocrat  
> Created at: 2023-06-20 18:21:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2697#issuecomment-1599288925  

Could you give some details I can provide that might be helpful? @klemens-morgenstern

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-06-21 01:23:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2697#issuecomment-1599931540  

How are you working in general? Callbacks? Coros?

---

## Comment 4

> Username: systocrat  
> Created at: 2023-06-22 12:23:52 UTC  
> Updated at: 2023-06-22 12:30:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2697#issuecomment-1602545452  

It's a combination of both, callbacks where one of the following is true:  
- It's a one-off that we don't care about when it finishes in the initiating code / only want to log if there is an error (and the callback will have access to our logging operation)  
or  
- It's a repeating operation that can start or stop at any time, depending on external factors (for example a queue containing bytes to write to a socket- empty the queue then deschedule until another write is requested)  
  
And coroutines where the sequence of events is both nontrivial and serial- so most of the business logic is written this way.  
  
I have many objects that are managed by shared_ptrs that initiate async operations, and they have members of their own also managed by shared_ptrs and initiate their own async operations as well.  
  
Ideally, I'd like to no longer nest shared_ptrs, since it seems redundant, but the problem is that a parent object that is managed by a shared_ptr might go out of scope and be destructed. This leaves member objects that would otherwise be managing their own lifetime with shared_ptr with outstanding async operations still running high and dry with pointers to reclaimed memory as data. So what's the solution?

---

## Comment 5

> Username: systocrat  
> Created at: 2023-06-22 14:32:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2697#issuecomment-1602748272  

@klemens-morgenstern   
Okay, I think I'd like to have all async operations from an objects members be associated with the owning parent objects shared_ptr.  
  
I can write my own completion handler that wraps another one, and holds a shared_ptr to the operation owner in its state- is this a good solution?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-06-22 15:13:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2697#issuecomment-1602819584  

You don't even need to write that if you're on a recent boost version, you can use [asio::consign](https://www.boost.org/doc/libs/1_82_0/doc/html/boost_asio/reference/consign.html) for that.

---

## Comment 7

> Username: systocrat  
> Created at: 2023-06-22 16:03:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2697#issuecomment-1602929646  

Oh, that's perfect, thanks!
