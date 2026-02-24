# #1898 - Calling beast::tcp_stream::cancel() triggers application termination [Closed]

> Username: jade2k11598  
> Created at: 2020-04-10 05:32:01 UTC  
> Updated at: 2020-04-10 17:58:11 UTC  
> Closed at: 2020-04-10 17:56:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1898  

Hi,  
I'm using boost 1.72.  Due to some requirements, I need to be able to cancel a session on the client end, regardless of what's it's doing.  So the session class has a `beast::tcp_stream`:  
```  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::resolver resolver_;  
    beast::tcp_stream stream_;  
...  
public:  
    // Objects are constructed with a strand to  
    // ensure that handlers do not execute concurrently.  
    explicit  
    session(net::io_context& ioc)  
        : resolver_(net::make_strand(ioc))  
        , stream_(net::make_strand(ioc))  
    {  
    }  
```  
Following from an example given [here](https://github.com/boostorg/beast/issues/940#issuecomment-352183929), this session class has a public member function `do_cancel` as follows:  
```  
    void do_cancel()  
    {  
        std::cout << "posting a cancel" << std::endl;  
  
        boost::asio::post(  
            stream_.get_executor(),  
            boost::asio::bind_executor(  
                stream_.get_executor(),   // returns strand used by stream  
                [&]()  
                {  
                    stream_.cancel();  
                }));  
    }  
```  
The main thread has access to the instance of this session and triggered this `do_cancel()` function, while another thread was in the middle of posting a large file (middle of doing an `http::async_write`) using the same session and the application aborted (termination triggered).    
  
Am I doing something wrong in my call to `boost::asio::post`?  My understanding with strands is that all tasks submitted for posting using the same strand, will serialize the processing of all tasks.  The call to `stream_.get_executor()` returns the same strand used by the stream, correct?  So I'm puzzled by what's triggering this termination ...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-10 09:09:58 UTC  
> Updated at: 2020-04-10 09:10:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1898#issuecomment-611951140  

Try:  
```  
        boost::asio::post(  
            boost::asio::bind_executor(  
                stream_.get_executor(),   // returns strand used by stream  
                [&]()  
                {  
                    stream_.cancel();  
                }));  
```

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-04-10 10:45:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1898#issuecomment-611980404  

You probably want to capture `[self = this->shared_from_this()]` in the lambda capture also, otherwise you'll get UB if there is a crossing case in the invocations of the completion handlers of the cancellation post and the async_read/writes

---

## Comment 3

> Username: jade2k11598  
> Created at: 2020-04-10 14:08:28 UTC  
> Updated at: 2020-04-10 14:33:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1898#issuecomment-612044171  

So I tried this:  
```  
    void do_cancel()  
    {  
        std::cout << "posting a cancel" << std::endl;  
  
        boost::asio::post(  
            boost::asio::bind_executor(  
                stream_.get_executor(),  
                [self=this->shared_from_this()]()  
                {  
                    self->stream_.cancel();  
                }));  
    }  
```  
and it still terminates.  I purposely had executed the `do_cancel()` in the middle of a large `async_write` (writing a `http::file_body`) to test if it causes problems, but it appears to crash the executable.  
  
And yes, I do have all the `async_read`/`async_write` pass the `shared_from_this()`:  
```  
http::async_write(stream_, *sr_,  
                beast::bind_front_handler(  
                &session::on_write_file,  
                shared_from_this()));  
```  
Normal operations works fine.  But the minute `do_cancel()` is executed, that's when it crashes.  It'll printout the "posting a cancel" but follows it with a termination.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-04-10 17:39:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1898#issuecomment-612138070  

It's difficult to help without seeing a complete example which exhibits the problem. Are you in a position to create an MCVE and share it with me on github? Something that allows me to (more or less) deterministically recreate the problem?  
  
I've written a number of systems with asio which have this cancellation functionality. I expect the problem will become apparent fairly quickly.  
  
It sounds like a race (or crossing case) but it's difficult to tell without compiling something up.

---

## Comment 5

> Username: jade2k11598  
> Created at: 2020-04-10 17:56:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1898#issuecomment-612145097  

Sorry, I wasn’t trying to simplify a version that I could pass on, but as I did, I figured out the problem!  I back tracked the changes I did and it looks like there was an error with my code.  Prior to providing the `do_cancel()` function, I also had updated the client to run multiple threads with `io_context` and let's just say the managing of those threads were not done correctly (the simple test I did was submit a large `async_write`, then cancel after a millisecond, then have it exiting the program).  Once I fixed the thread management with `executor_work_guard` among other things, then the cancel worked fine and there was no termination.  I think the termination was due to a combination of threads dying prematurely and/or then not going down nicely.  
  
I'll go ahead and close this ticket.  Sorry for wasted time.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-04-10 17:58:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1898#issuecomment-612145919  

No fixed bug is wasted time.  
  
Glad you found it. No problem at all with the time spent. That's what I'm here for.
