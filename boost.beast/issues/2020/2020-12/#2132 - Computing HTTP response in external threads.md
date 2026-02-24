# #2132 - Computing HTTP response in external threads [Closed]

> Username: claudv  
> Created at: 2020-12-02 15:44:05 UTC  
> Updated at: 2022-01-09 05:17:04 UTC  
> Closed at: 2022-01-09 05:17:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2132  

I am working to implement an HTTP server that interacts with an external "calculation engine" to calculate the body of HTTP responses. The calculation engine manages its own thread pool, separate from the `io_context` pool.  
  
My goal is to have a server that   
- reads a HTTP request  
- submits asynchronous work to the calculation engine  
- waits to be notified upon calculation completion with the necessary data to assemble the response body  
- sends the response back  
  
The approach I am following consist of submitting request data to a worker thread (ie, the engine) along with a completion handler to be invoked by the worker thread at the end. The completion handler wraps `asio::post` (necessary for thread safety) to schedule an `async_write`, and retains ownership of whatever necessary, like http_session.   
  
I have put together a bare bone prototype starting from the given http examples. All the relevant adaptation happens inside `HttpSession::onRead`, which I share below:   
  
  
```  
void HttpSession::onRead(beast::error_code ec, std::size_t bytes_transferred) {  
    boost::ignore_unused(bytes_transferred);  
  
    // This means they closed the connection  
    if (ec == http::error::end_of_stream) return doClose();  
  
    if (ec) return logFailure(ec, "read");  
  
    // completion handler to be invoked by an external worker thread once the calculation is done.  
    auto sendResponse = [self = shared_from_this()](std::string responseBody, auto&& req) {  
  
        http::response<http::string_body> res;  
        res.version(11);  
        res.result(http::status::ok);  
        res.content_length(responseBody.size());  
        res.set(http::field::content_type, "text/plain");  
        res.body() = std::move(responseBody);  
        res.prepare_payload();  
        res.keep_alive(req.keep_alive());  
  
        // using post since we are notifying io_context from some external thread  
        net::post(  
                self->stream_.get_executor(),  
                [self = std::move(self), res]() mutable {  
                    // queue_ calls async_write internally   
                    self->queue_(std::move(res));  
                }  
        );  
  
    };  
  
    // perform async work on some external non-io_context thread  
    if (_thread.joinable()) _thread.join();  
  
    _thread = std::thread([sendResponse, req = parser_->release()]() {  
  
        // compute response body, in reality some logic that uses req  
        std::string responseBody = req.body();  
  
        // simulate a time consuming calculation  
        std::this_thread::sleep_for(std::chrono::seconds(2));  
  
        sendResponse(responseBody, req);  
    });  
  
  
    // If we aren't at the queue limit, try to pipeline another request  
    if (!queue_.isFull()) doRead();  
}  
```  
Happy to share more code if needed, but the rest is pretty much unchanged from the canonical examples.  
  
While the implementation seems to work as  expect, my question is:  
is the approach fundamentally correct?  
is this the correct/good practice?  
Am I reinventing some wheel?  
  
Thanks

---

## Comment 1

> Username: djarek  
> Created at: 2020-12-02 18:53:53 UTC  
> Updated at: 2020-12-02 18:56:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2132#issuecomment-737427440  

Ideally you should use a separate executor to run blocking work (so that you don't block your I/O context). Here's a simplified example of how you can achieve it:  
https://godbolt.org/z/3GP63s  
  
It works like this:  
1. Jumps to the "work" executor using `post`  
2. Does the work and stores the result.  
3. Jumps back to the executor associated with the callers's completion token.  
4. Invoke the caller's completion handler with the result (if any).  
  
Note that the example is missing a resource limit, you may want to respond with an error if all computational resources are already in use.  
  
Warning: In order for this to work, you need to make sure that the token has an associated executor, otherwise, the handler may be invoked on the wrong thread!

---

## Comment 2

> Username: claudv  
> Created at: 2020-12-02 20:55:27 UTC  
> Updated at: 2020-12-02 21:16:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2132#issuecomment-737489844  

Very interesting example, thanks, I can learn a lot by studying it in detail.  
  
If I understand correctly your solution assumes the 'worker pool' to be another asio pool, whereby I cannot use this if my worker pool is an entirely different library. My issue is more about interoperation strategies between the asio/beast concurrent model and external threads.  
  
I realize my doubts boil down to this question: can `post` be safely called from _any_ thread in my application (including threads outside of io_context) as far as it captures the asio executor of the context I want to post to?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-12-08 17:50:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2132#issuecomment-740797152  

You are capturing `res` by value in your lambda, which makes an unnecessary copy.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2132#issuecomment-850857928  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2132#issuecomment-1008232341  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
