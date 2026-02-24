# #1799 - Assertion sometimes fails at BOOST_ASSERT(! state.timeout)? [Closed]

> Username: nosamproductions  
> Created at: 2020-01-06 20:54:08 UTC  
> Updated at: 2020-02-07 17:05:20 UTC  
> Closed at: 2020-02-07 17:05:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1799  

I have an HTTPS server built off beast. At seemingly random times, an assertion inside timeout_handler (within basic_stream.hpp) that asserts that state.timeout is false fails, and kills my server. My program is basically the example HTTPS server with POST handling added, and I'm really not sure what the source of the issue is. I provided some code that may be related.  
  
Does anybody have any idea how it would be possible for state.timeout to sometimes fail to be reset to "false"? Looking through the code the timeout appears to only be set directly after this assertion, and in two other areas within basic_stream.hpp. I cannot seem to find anybody else experiencing this error. Could it have something to do with using a do_close() function? Or could it have something to do with using a thread pool?  
  
I have been struggling with this all day and would appreciate any input!  
  
### beast code where assertion fails:  
  
  
```  
template<class Protocol, class Executor, class RatePolicy>  
struct basic_stream<Protocol, Executor, RatePolicy>::  
    timeout_handler  
{  
    op_state& state;  
    boost::weak_ptr<impl_type> wp;  
    tick_type tick;  
  
    void  
    operator()(error_code ec)  
    {  
        // timer canceled  
        if(ec == net::error::operation_aborted)  
            return;  
        BOOST_ASSERT(! ec);  
  
        auto sp = wp.lock();  
  
        // stream destroyed  
        if(! sp)  
            return;  
  
        // stale timer  
        if(tick < state.tick)  
            return;  
        BOOST_ASSERT(tick == state.tick);  
  
        // timeout  
        BOOST_ASSERT(! state.timeout); // <---- ASSERTION FAILS HERE  
        sp->close();  
        state.timeout = true;  
    }  
};  
```  
  
### Some server code  
  
Used in the async_write callback:  
  
```  
if (sp->need_eof())  
{  
	return session_ptr->do_close();  
	session_ptr->res_ = nullptr;  
} else {  
	session_ptr->res_ = nullptr;  
	session_ptr->do_read();  
}  
```  
  
async_read's callback includes these lines:  
  
```  
if (ec == boost::beast::http::error::end_of_stream || ec == boost::asio::error::connection_reset)  
	return do_close();  
```  
  
And do_close looks like:  
  
```  
void http_server_session::do_close()  
{  
	boost::system::error_code ec;  
	socket_.shutdown(tcp::socket::shutdown_send, ec);  
}  
```  
  
And my thread pool initialization looks like:  
  
```  
for (auto i = thread_count - 1; i > 0; --i) {  
		threads.emplace_back([&io_context] {  
		io_context.run();  
	});  
}  
```  
  
### Version of Beast  
  
I'm using Boost 1.70.0  
  
### All relevant compiler information  
  
Microsoft Visual Studio 2017

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-07 07:55:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-571477138  

Hi there @nosamproductions. Is there any chance you can put together a minimal complete compilable example that causes the problem to exhibit itself?   
I'm thinking a minimal client and server that chats to itself until the problem is realised.  
I appreciate that this will involve a little work, but it would definitively establish:  
* Whether there is a problem in beast  
* A means to reproduce the problem in a controlled environment.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-01-07 14:33:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-571611816  

Does the problem go away when you make your `io_context` single-threaded?

---

## Comment 3

> Username: nosamproductions  
> Created at: 2020-01-08 15:24:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-572119037  

This morning the error has occurred less frequently, making it harder to compare single-threaded to multi-threaded. Perhaps the error occurs because of a race condition that exists somewhere? I'm thinking the error might be occurring less frequently now because my computer has been restarted and is a bit faster. I'm struggling to trigger it.  
  
The most consistent way to trigger an assertion so far has been to place a breakpoint on the line with this assertion described in my original post, and randomly sometimes pressing "continue" in the error dialogue leads immediately to the assertion failing, though the assertion that fails is in a different thread and the assertion itself is on a different line of code further down in basic_stream.hpp:  
  
```  
if(state().timer.expiry() != never())  
	{  
	++state().tick;  
  
	// try cancelling timer  
	auto const n =  
		state().timer.cancel();  
	if(n == 0)  
	{  
		// timeout handler invoked?  
		if(state().timeout)  
		{  
	// yes, socket already closed  
	ec = beast::error::timeout;  
	state().timeout = false;  
		}  
	}  
	else  
	{  
		BOOST_ASSERT(n == 1);  
		BOOST_ASSERT(! state().timeout); // <---- ASSERTION FAILS HERE  
	}  
}  
```  
  
I think I when I first made this post I wasn't aware that the assertion was two assertions in different places checking the same condition. The assertion I described in the initial post on this thread seems to be something that I cannot trigger but that occurs randomly on connection timeouts (but currently doesn't trigger at all, even though I haven't been changing code).  
  
Two important pieces of information I forgot to add is that this error is occurring in an environment where the server is receiving a client request every second (the client is making heartbeat requests) AND after either of the two assertions fails, until I hit Retry/Abort/Ignore on the error popup, the server appears to continue handling the heartbeats just fine, receiving client requests and providing a response. It doesn't appear to break the server's ability to communicate, at least for a short period of time.  
  
>Does the problem go away when you make your io_context single-threaded?  
  
No matter what I try, so far I cannot trigger any kind of assertion failure with a single-threaded io_context. I'm not sure how this code is designed to work so I don't know what protections/locks there are in place to prevent simultaneous access to variables, but I think this is may be some kind of threading related issue, I'm wondering: is a thread changing state.timeout to "true" before it is evaluated in an assertion in a different thread? Or am I somehow doing something on my end to trigger this strange behavior? I know for a fact I am not directly accessing the state.timeout variable in my own code.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-01-08 15:25:53 UTC  
> Updated at: 2020-01-08 15:26:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-572119707  

> I think this is may be some kind of threading related issue  
  
Are you using a strand?

---

## Comment 5

> Username: nosamproductions  
> Created at: 2020-01-08 15:38:21 UTC  
> Updated at: 2020-01-08 15:42:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-572125417  

Feeling pretty dumb right now, that seems like it may be the issue. I seem to recall not using those when first writing this a long time ago thinking they were unneeded. Woops. I guess I just need to use make_strand with the io_context when initializing the acceptor?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-01-08 16:39:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-572152983  

I/O objects (such as sockets) are *not thread safe*. You must ensure that the same instance of an I/O object is not accessed concurrently. Explicit strands are one way to do that. Only using one thread with the `io_context` is another. There are more ways.

---

## Comment 7

> Username: nosamproductions  
> Created at: 2020-01-08 16:44:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-572155299  

Ok, I will implement strands into my server and see how it goes, thank you very much! Definitely wont be repeating this mistake.

---

## Comment 8

> Username: stale[bot]  
> Created at: 2020-02-07 17:02:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-583501541  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: nosamproductions  
> Created at: 2020-02-07 17:05:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1799#issuecomment-583502977  

Issue resolved
