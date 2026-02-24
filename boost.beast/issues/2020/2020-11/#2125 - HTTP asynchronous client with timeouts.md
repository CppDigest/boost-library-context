# #2125 - HTTP asynchronous client with timeouts [Closed]

> Username: ropieur  
> Created at: 2020-11-23 09:15:31 UTC  
> Updated at: 2021-01-08 12:44:13 UTC  
> Closed at: 2021-01-08 12:39:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2125  

Hello,  
  
I'd like to implement an asynchronous HTTP client with different timeouts and I would need some guidance. Typically, I'd like to have a connection timeout, a request/response timeout, but also a shorter timeout while receiving very large response which is renewed as long as data is received. I am wondering whether it could be easily feasible with beast tcp_stream only. What kind of timing aspect does tcp_stream allow? Would I need additional timer(s) to achieve my goal?  
  
(NB: I don't use coroutines yet)  
  
Thank you

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-11-23 11:04:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2125#issuecomment-732089955  

The short answer here is that at the same time as initiating the asynchronous io (e.g. async_read), you would also initiate an asynchronous wait on a timer. Whichever finishes first calls `cancel` on the other.  
  
Both will complete. Whichever completes first is the one that makes the decision on what the next step will be.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-11-23 11:28:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2125#issuecomment-732102194  

Here's a simple example (not tested):  
  
https://godbolt.org/z/j7Wxdv  
  
```  
  
#include <boost/asio.hpp>  
#include <cassert>  
  
namespace net = boost::asio;  
using boost::system::error_code;  
  
  
struct test_object : std::enable_shared_from_this<test_object>  
{  
    using executor_type = net::io_context::executor_type;  
  
    // if multithreaded, make sure both timer and socket are on the same strand.  
    // if single-threaded, just make sure they are on the same executor  
    test_object(net::io_context::executor_type e)  
    : my_strand_(net::make_strand(e))  
    , sock_(get_executor())  
    , timer_(get_executor())  
    {  
        assert(sock_.get_executor() == timer_.get_executor());  
        assert(get_executor() == timer_.get_executor());  
    }  
  
    void initiate_read()  
    {  
        async_op_error_.clear();  
        timer_.expires_after(read_timeout());  
        timer_.async_wait([self = shared_from_this()](error_code ec)  
        {  
            if (ec == net::error::operation_aborted)  
            {  
                // means the timer was cancelled. i.e. no timeout  
            }  
            else if (ec)  
            {  
                // some other error - this would be fatal  
                if (!self->async_op_error_)  
                    self->async_op_error_ = ec;  
                self->sock_.cancel();  
            }  
            else  
            {  
                // timer timed out - so we want to error out the socket  
                ec = net::error::timed_out;  
                if (!self->async_op_error_)  
                    self->async_op_error_ = ec;  
                self->sock_.cancel();  
            }  
  
            if(-- self->ops_waiting_ == 0)  
                self->on_read_complete(self->async_op_error_);  
        });  
  
        auto buf = net::dynamic_buffer(read_buffer_);  
        auto orig = buf.size();  
        buf.grow(65536);  
        sock_.async_read_some(buf.data(orig, buf.size()-orig),   
        [self = shared_from_this(), buf](error_code ec, std::size_t bytes_transferred) mutable  
        {  
            buf.shrink(65536 - bytes_transferred);  
            self->timer_.cancel();  
  
            if (ec)  
            {  
                if (!self->async_op_error_)  
                    self->async_op_error_ = ec;  
            }  
  
            if(--self->ops_waiting_)  
                self->on_read_complete(self->async_op_error_);  
        });  
  
  
        ops_waiting_ += 2;  // one timer, one socket  
    }  
  
    void on_read_complete(error_code ec)  
    {  
        // decide what to do next  
        if (ec)  
        {  
            // read failed or timed out  
        }  
        else  
        {  
            // process data and initiate next read  
            process_data();  
            initiate_read();  
        }  
    }  
  
    void process_data()  
    {  
  
    }  
  
    std::chrono::milliseconds read_timeout()  
    {  
        // 10 second timeout  
        return std::chrono::milliseconds(10000);  
    }  
  
    auto get_executor() const -> net::strand<executor_type> const&  
    {  
        return my_strand_;  
    }  
  
    net::strand<executor_type> my_strand_;  
    net::ip::tcp::socket sock_;  
    net::steady_timer timer_;  
  
    std::string read_buffer_;  
    error_code async_op_error_;  
    std::size_t ops_waiting_ = 0;  
};  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-11-23 11:48:25 UTC  
> Updated at: 2020-11-23 11:49:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2125#issuecomment-732112377  

The OP Is asking about Beast's stream, which has a built-in timer. The answer is, yes it is possible and no you do not need a separate timer. Use `http::async_read_some` in a loop and set the timeout for each call.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-11-23 19:39:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2125#issuecomment-732382947  

Ah yes.  
  
Beast’s basic_stream will handle the timeout for you. Bear in mind that the timeout is for the complete read, not for each placket.  
  
If you’re planning on downloading very large documents you may want to use multiple async_read operations with a limit on the size read.  
  
For most documents the stream will work perfectly well as it is.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-12-25 12:12:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2125#issuecomment-751240892  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: ropieur  
> Created at: 2021-01-08 12:41:48 UTC  
> Updated at: 2021-01-08 12:42:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2125#issuecomment-756736028  

Didn't really have the time to implement a solution based on the proposal of @vinniefalco, but I consider this fixed.

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-01-08 12:44:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2125#issuecomment-756737065  

Just looking at the question again, you can split the read into a read of  
the header followed by a read of the body.  
The content size reported by the header can inform the timeout you choose  
for reading the body.  
  
If the response is chunked then you might want to choose an appropriate  
timeout for reading each chunk.  
  
  
On Fri, 8 Jan 2021 at 13:42, ropieur <notifications@github.com> wrote:  
  
> Didn't really have the time to implement a solution based on the proposal  
> of @vinniefalco <https://github.com/vinniefalco>, but I consider this  
> fixed.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2125#issuecomment-756736028>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSLNRB46BXAW4KG4G4TSY34RVANCNFSM4T7GX3LA>  
> .  
>  
  
  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212
