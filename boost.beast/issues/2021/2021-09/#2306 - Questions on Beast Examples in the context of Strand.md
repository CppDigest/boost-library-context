# #2306 - Questions on Beast Examples in the context of Strand [Closed]

> Username: jade2k11598  
> Created at: 2021-09-02 16:45:28 UTC  
> Updated at: 2021-09-07 17:15:32 UTC  
> Closed at: 2021-09-07 16:52:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2306  

So in several [boost beast examples (boost 1.72)](https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/examples.html), it makes mention of using strands to ensure handlers do not execute concurrently.  For example (in `http_client_async.cpp`):  
```  
// Performs an HTTP GET and prints the response  
class session : public std::enable_shared_from_this<session>  
...  
    // Objects are constructed with a strand to  
    // ensure that handlers do not execute concurrently.  
    explicit  
    session(net::io_context& ioc)  
        : resolver_(net::make_strand(ioc))  
        , stream_(net::make_strand(ioc))  
    {  
    }  
```  
In the context of an asio that involves a pool of threads, the writing to this `stream_ `(as shown below) if executed over different threads is executed concurrently:  
```  
        // Send the HTTP request to the remote host  
        http::async_write(stream_, req_,  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
```  
Now, my understanding of boost `async_write` and `async_write_some`, is that the `async_write` doesn't trigger the completion handler until all the contents of the buffer has been written to the socket, whereas the `async_write_some` could trigger the completion handler without having written the full contents of the buffer to the socket.  
  
From what I've [read elsewhere ](https://www.py4u.net/discuss/73959) that when the buffer size exceeds 65536 bytes, the `async_write` underneath, triggers  `async_write_some` calls and can run concurrently if it has to be called more than once.  
  
But the fact that the `stream_` was created with `make_strand` and these series of underlying `async_write_some` still has to write to that same `stream_`, wouldn't the underlying `async_write_some` calls be also executed serially when writing to the `stream_`?  
  
I ask this because as a test, I attempted to write a buffer size larger than 65536 bytes using `boost::asio::async_write` (which I don't think is much different than the `http::async_write` version) to a `stream_` that was constructed with `make_strand` and the completion handler returned an error of `Broken pipe`, noting that it only wrote 65536 bytes of a buffer size of 1Mbytes.    
  
My guess is that the underlying `async_write_some` that were triggered were not serialize.  But the question is, why not, when the `stream_` was constructed with `make_strand`?  Am I misunderstanding how strand works?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-09-02 17:10:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2306#issuecomment-911891757  

There is a common misconception about the role of the strand in protecting streams from multiple concurrent async operations.  
The fact is that these things are not related.  
  
You can think of a strand as a wrapper around an executor that prevents more than one completion handler (whether intermediate or final) from executing concurrently on the same executor. In effect, it is logically a mutex:  
  
```  
io_context ioc;  
auto e = ioc.get_executor();  
post(e, f1);  
post(e, f2);  
```  
  
In the above case, it is conceivable that f1 and f2 will make progress concurrently. This could happen if for example, there is more than one thread calling `io_context::run()` on ioc.  
  
However:  
```  
io_context ioc;  
auto e = make_strand(ioc.get_executor());  
post(e, f1);  
post(e, f2);  
```  
In this case, f1 and f2 will never execute concurrently. Because `e` is now a strand wrapping the io_context's executor. f2 will remain in the io_context's execution queue until f1 has returned.  
  
Note that all async_xxx completion handlers are executed _as if by_ `post()`. So if they are associated with strand, they will never execute concurrently. This solves the problem of mutual exclusion.  
  
However, there is a further restriction on asio streams. Not only must access to the stream object be protected against concurrent (thread) access, you must also not initiate more than one async_read or async_write at a time.  
  
The problem is user code is normally with the write. Because new users of asio expect to be able to throw multiple async_write operations at a stream and assyme that the stream will take care of the queuing.   
  
This is not the case.  
  
An async_write _initiation function_ must not be called while another async_write is in progress - that is, until the outstanding completion handler has been called - which will happen _as if by post to the associated executor_.  
  
You are correct in your understanding that async_write is implemented in terms of the sequential initiation of an unbounded number of async_write_some operations. The intermediate completion handlers for these sub-operations will be executred on the associated executor, according to the rules of that executor (see strand).  
  
The broken_pipe error is interesting. It implies that the other end hung up. This can be for any number of reasons. I would need to see a more complete example (ideally one that compiles and runs without me having to make any edits) before I could comment.  
  
Are you able to supply a minimal complete example that consistenly exhibits the problem? I'm sure there's a simple explanation. It will most likely be related to the text above :)

---

## Comment 2

> Username: jade2k11598  
> Created at: 2021-09-07 16:52:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2306#issuecomment-914466577  

Sorry for the delayed response (was away on holiday).  So as I was working on stripping proprietary code out of my test case, I just realized that there was an error in my code.  The buffer that was passed into `async_read_until` _was local_, which explains why the async_write was only able to write a limited amount of buffer.  
  
Once I fixed the `async_read_until` part (having the buffer passed to it to outlive the async function call), then the `async_write` had no issue, transferring the full buffer of 1Mbyte and `async_read_until` reading the full buffer.    
  
Sorry for wasting your time and thanks for the detail discussion on strands.  I'll go ahead and close this ticket.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-09-07 17:15:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2306#issuecomment-914482122  

Thank you for reporting back with a detailed discovery - and for closing the issue.  
  
It is never a waste of my time if that time results in our users fixing bugs in their code.  
  
My pleasure. Please get in touch again if you have any further problems.
