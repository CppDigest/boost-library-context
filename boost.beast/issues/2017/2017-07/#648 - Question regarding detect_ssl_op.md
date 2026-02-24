# #648 - Question regarding detect_ssl_op [Closed]

> Username: akoolenbourke  
> Created at: 2017-07-13 15:08:34 UTC  
> Updated at: 2017-07-16 23:40:48 UTC  
> Closed at: 2017-07-16 23:40:48 UTC  
> Url: https://github.com/boostorg/beast/issues/648  

Hi there.  
  
I was looking through the detect_ssl composed operation and noticed this line in the operator()  
  
`return stream_.get_io_service().post(  
   bind_handler(std::move(*this), ec, 0));  
`  
I realise (As stated in the comments) that this needs to happen because handlers can't be called before the initiating function returns. However, it is possible that immediately after the post(), a context switch happens, "pausing" the return operation and a new io.run() worker thread picks up the post and processes it.  
  
I realise that in most (all?) situations this would likely have no real effect but does this violate the guarantee and is it of any concern?  
  
Thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-13 17:40:13 UTC  
> Updated at: 2017-07-13 17:46:20 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315149888  

Great question!! Yes that can happen, and it **will** happen, which means you need either an implicit or explicit strand:  
  
* Implicit strand: only one thread calling `io_service::run`  
  
* Explicit strand: good ole' `io_service::strand`  
  
* Your own invocation strategy (hey you never know)  
  
`beast::bind_handler` preserves the invocation hooks and forwards them to the underlying handler, so if `*this` invokes using a strand, then `bind_handler(std::move(*this), ...)` will also invoke using the same strand.

---

## Comment 2

> Username: akoolenbourke  
> Created at: 2017-07-13 22:04:11 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315214950  

Thanks Vinnie.   
  
So, this does raise the question of performance.  
  
I am currently going through an experimental phase to see how ASIO/Beast might work for us (We have a big network product and are looking to re-architect our IO) and it seems that the canonical way to build a pipeline of processing stages would be with composed operations.   
  
A thought experiment would have me developing a simple HTTP Tunnel (Via CONNECT). Using async_detect_ssl as an example I could have an async_detect_connect and the invocation of that might complete immediately if I  found a CONNECT verb in the buffer already. I would then call it's handler which might be an async_tunnel_data to start moving data from one socket to the next. This tunnel handler (Composed operation) would also need to be in a strand correct, because it was potentially invoked immediately in async_detect_connect. Thereby pushing the strand down the chain.  
  
Am I thinking about this the right way or have I left planet earth?  
  
Thanks and great work on Beast BTW.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-13 22:14:05 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315216718  

>it seems that the canonical way to build a pipeline of processing stages would be with composed operations.  
  
This is the best in terms of performance but I'll warn you up front, writing composed operations requires particular skill acquired through trial and error. I've tried to polish those rough edges off as much as I could, with the tutorial in the documentation and also the set of helpers for composed operations.  
  
You don't need to write a composed operation to get the CONNECT string, you can write this instead:  
```  
flat_buffer b;  
request<empty_body> req;  
read_header(sock, b, req);  
if(req.method() == verb::connect)  
    do_tunnel(sock);  
// something else  
```  
  
The code above uses the synchronous interface but you can do the same thing using asynchronous calls.    
  
Where you **do** want a composed operation is your implementation of the relay function performed by your tunnel. Read from the downstream connection, write to the upstream connection, and vice versa. Probably best to implement that with one operation taking two sockets and then launch it twice (the second time with the order of sockets reversed).  
  
When you write the composed operation you dont have to worry about the strand at all, you just have to follow the rules for composed operations:  
  
* Don't type-erase the handler  
* Forward the hooks (allocate, deallocate, invoke, is_continuation)  
* Use the composed operation as the completion handler for intermediate operations (e.g. `std::move(*this)`)  
* Manage complex state using `beast::handler_ptr`, simple state using a trivially copyable/movable composed operation.  
  
You only see or worry about the strand at the highest level when you make your call to the initiating function, for example:  
```  
async_tunnel_some(from, to, strand_.wrap(  
    std::bind(&connection::on_header, shared_from_this(), std::placeholders::_1)));  
```  
  
As long as your initiating function and associated composed operation follows the rules, everything will Just Work

---

## Comment 4

> Username: akoolenbourke  
> Created at: 2017-07-13 22:59:19 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315224604  

Thanks  
  
Sorry if I wasn't clear; the use of the strand at the highest level is the actual concern I have (re performance). Initiating with a strand implies that all of the operations within the composed operation will use that strand automatically because the handlers for intermediate calls will use something like std::move(*this). Now I realise that typically the operations in a composed operation are performed one after the other in a state machine and therefore handler concurrency is non existent, but is there any significant overhead to the fact that the handler needs to now go in a queue for the strand?  
  
Also, I'm starting to wonder if we're talking at crossed purposes a little.  
  
I understand the need for strands when you're firing off two simultaneous async operations and need to access shared state in the handlers. My issue is due to the race condition that seems to occur between the post() in immediately returning composed operations and the high level caller. e.g.  
  
```  
void guy::somefunc()  
{  
    async_do_something(socket, [this](error_code ec) // async_do_something completes immediately and posts  
    {  
       member_ = 42;  // This could be executed before async_do_other is called  
    });  
    
    async_do_other(second_socket,[this](error_code ec)  
    {  
        // code here never touches member_  
    });  
  
  // bunch of other code that never touches member_  
    someothermember_ = 0;  
}  
  
```  
  
In the above code, would I even need to bother with a strand in the initiating function in the first place, even though it might return AFTER the handler is called. i.e. Is the only real requirement that you don't call handlers from within initiating functions.  
  
Is that clear as mud now?  
  
Cheers

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-13 23:22:41 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315228183  

I think you're worrying too much about a hypothetical race condition that doesn't exist. If a composed operation already on the strand posts to the same strand, the posted operation is appended to the queue associated with the strand and executed when the calling function returns, see:  
  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/impl/strand_service.ipp#L40

---

## Comment 6

> Username: akoolenbourke  
> Created at: 2017-07-14 00:23:06 UTC  
> Updated at: 2017-07-14 00:26:06 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315236497  

Yeah I realise how the stranding works I just don't want to strand things I don't need to (there's locks in there etc). In the example I provided, I just wanted to clarify whether I needed to strand when a CO posts() immediately even though outside I'm not going to access shared resources.  
  
Thanks for answering all my questions

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-07-14 00:40:04 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315238518  

> I just wanted to clarify whether I needed to strand when a CO posts() immediately even though outside  
  
How would the initiating function know if the composed operation will complete immediately? If it knew that, it could skip the initiating function and just do the operation.  
  
@chriskohlhoff do you have anything you can add to this?

---

## Comment 8

> Username: akoolenbourke  
> Created at: 2017-07-14 01:14:14 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315242779  

The way I was interpreting your comments is that I should ALWAYS use strand.wrap when calling an initiating function if the composed operation might return immediately (Would need to be documented per composed operation). And this was to avoid the race condition I raised in the initial post; that the post()ed handler gets called before the initiating function returns,.  
  
However, is that even a concern? If, after calling the composed_op{}(), all the initiation function is doing is init.result.get() and returning to the initiating function that returns immediately to client code, does that matter if the handler was called before the init.result.get()? If not I would assume a strand is not necessary UNLESS you want to make another async operation from client code that has a handler that might access shared state and don't want it executing at the same time as the initial one.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-07-14 01:53:41 UTC  
> Updated at: 2017-07-14 01:54:12 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315247915  

I'm not saying to always use `strand.wrap()`. I am saying that you must ensure that one of the following conditions is true:  
  
* Every completion handler is protected by an *implicit strand*: only one thread calling `io_service::run`, OR  
  
* Every completion handler is protected by an *explicit strand* (`io_service::strand`), OR  
  
* Every completion handler uses a custom invocation hook provided by the caller to ensure that only safe accesses are made to the underlying stream or socket object (such objects are *not* thread safe; they may not be accessed concurrently from multiple threads). i.e. an overload of `asio_handler_invoke` for your user defined type.  
  
> if the composed operation might return immediately  
  
You are saying "might" which is the same as saying that the composed operation may call an intermediate initiating function. In this case, the strand is necessary.  
  
If instead you mean "the composed operation always returns immediately" (as in, the composed operation always calls `post` with an immediate result instead of calling another initiating function) then what you have is not a composed operation at all but rather a function that performs some work and incurs the unnecessary expense of dispatching its result. I don't suppose you mean this one because it doesn't make sense.  
  
As I said before, I think you're overthinking this. Write the composed operation as per best practices, and then at call sites use the strand if more than one thread is calling `io_service::run`.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-07-14 01:56:34 UTC  
> Updated at: 2017-07-14 04:33:18 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315248328  

Perhaps you can demonstrate by posting sample code for a composed operation? Here's a simple one:  
```  
  template <typename AsyncReadStream,  
      typename CompletionCondition, typename ReadHandler>  
  class read_op<AsyncReadStream, boost::asio::mutable_buffers_1,  
      CompletionCondition, ReadHandler>  
    : detail::base_from_completion_cond<CompletionCondition>  
  {  
  public:  
    read_op(AsyncReadStream& stream,  
        const boost::asio::mutable_buffers_1& buffers,  
        CompletionCondition completion_condition, ReadHandler& handler)  
      : detail::base_from_completion_cond<  
          CompletionCondition>(completion_condition),  
        stream_(stream),  
        buffer_(buffers),  
        start_(0),  
        total_transferred_(0),  
        handler_(BOOST_ASIO_MOVE_CAST(ReadHandler)(handler))  
    {  
    }  
  
#if defined(BOOST_ASIO_HAS_MOVE)  
    read_op(const read_op& other)  
      : detail::base_from_completion_cond<CompletionCondition>(other),  
        stream_(other.stream_),  
        buffer_(other.buffer_),  
        start_(other.start_),  
        total_transferred_(other.total_transferred_),  
        handler_(other.handler_)  
    {  
    }  
  
    read_op(read_op&& other)  
      : detail::base_from_completion_cond<CompletionCondition>(other),  
        stream_(other.stream_),  
        buffer_(other.buffer_),  
        start_(other.start_),  
        total_transferred_(other.total_transferred_),  
        handler_(BOOST_ASIO_MOVE_CAST(ReadHandler)(other.handler_))  
    {  
    }  
#endif // defined(BOOST_ASIO_HAS_MOVE)  
  
    void operator()(const boost::system::error_code& ec,  
        std::size_t bytes_transferred, int start = 0)  
    {  
      std::size_t n = 0;  
      switch (start_ = start)  
      {  
        case 1:  
        n = this->check_for_completion(ec, total_transferred_);  
        for (;;)  
        {  
          stream_.async_read_some(  
              boost::asio::buffer(buffer_ + total_transferred_, n),  
              BOOST_ASIO_MOVE_CAST(read_op)(*this));  
          return; default:  
          total_transferred_ += bytes_transferred;  
          if ((!ec && bytes_transferred == 0)  
              || (n = this->check_for_completion(ec, total_transferred_)) == 0  
              || total_transferred_ == boost::asio::buffer_size(buffer_))  
            break;  
        }  
  
        handler_(ec, static_cast<const std::size_t&>(total_transferred_));  
      }  
    }  
  
  //private:  
    AsyncReadStream& stream_;  
    boost::asio::mutable_buffer buffer_;  
    int start_;  
    std::size_t total_transferred_;  
    ReadHandler handler_;  
  };  
```

---

## Comment 11

> Username: akoolenbourke  
> Created at: 2017-07-14 03:06:51 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315257864  

Thanks Vinnie. Sorry for taking up so much of your time. I think we might need to move on for now as I'm obviously not understanding something deeper about handler invocation (I understand strands in their day to day usage) or not explaining myself clearly enough.  
  
Your last condition (two posts above) does clarify some things I *think* as I am taking from that that so long as the completion handler (Even if it's just a lambda, nothing fancier) does not screw with anything that the client code might be doing asynchronously then I'm OK.   
  
If I get around to creating a composed operation I'll return here and post it so hopefully that would make it clearer. It would be nice to get to the bottom of it as it will affect how I approach the whole thing.   
  
Thanks

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-07-14 04:33:57 UTC  
> Updated at: 2017-07-14 04:35:01 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315267429  

Well if you are using `ssl::stream` you still need the strand if you launch any asynchronous operations  
  
>Sorry for taking up so much of your time  
  
Its no problem at all, glad to help in any way!

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-07-16 23:40:48 UTC  
> Url: https://github.com/boostorg/beast/issues/648#issuecomment-315646400  

It looks like there's nothing actionable for me here so I'll go ahead and close the issue to keep things tidy, feel free to open a new one at any time!
