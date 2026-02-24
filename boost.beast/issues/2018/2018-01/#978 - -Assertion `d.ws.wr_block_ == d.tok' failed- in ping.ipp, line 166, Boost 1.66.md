# #978 - "Assertion `d.ws.wr_block_ == d.tok' failed" in ping.ipp, line 166, Boost 1.66 [Closed]

> Username: rberlich  
> Created at: 2018-01-12 17:28:53 UTC  
> Updated at: 2018-01-12 22:12:31 UTC  
> Closed at: 2018-01-12 21:13:01 UTC  
> Url: https://github.com/boostorg/beast/issues/978  

Apologies if I keep bombarding you with questions at the moment -- after a couple of hours playing with the code I could use some guidance.   
  
I am trying to implement a simplified ping-sequence, with a constant (un-prolonged) ping period. This is meant to work in the following way:  
  
- In the server session, the callback in ws.control_callback(some_callback) sets some internal flag indicating that the connection is alive, when a pong (and also a ping -- the peer might be sending its own pings) arrives.  
  
- The server-session run() starts the ping sequence with a function called async_start_ping(), then starts the accept()-sequence (similar to what is in your examples).   
  
- async_start_ping() sets the timer (expires_after() ...), then starts a ws.async_wait() with call-back "when_timer_fired()", sets an internal flag indicating that a ping is in progress, then does an ws.async_ping() with callback "when_ping_sent()".   
  
when_ping_sent() essentially does nothing (except for reacting to a possible error_code, which would indicate a stale connection and sets an internal flag).  
  
when_timer_fired() restarts the ping-sequence (i.e. calls async_start_ping() ), if a ping- or pong-frame has arrived in the meantime (i.e. if ws.control_callback(some_callback) has set the corresponding flag ). Otherwise (in particular if the internal flag still indicates that a ping-operation is still in progress (meaning, no answer has arrived), it marks the connection as "stale".  
  
This compiles fine, but I do get an assertion at run-time, which I quote here in full:  
  
Evaluator: /opt/boost/include/boost/beast/websocket/impl/ping.ipp:166: void boost::beast::websocket::stream<NextLayer, deflateSupported>::ping_op< <template-parameter-2-1> >::operator()(boost::beast::error_code, std::size_t) [with Handler = boost::asio::executor_binder<std::_Bind<void (async_websocket_server_session::*(std::shared_ptr<async_websocket_server_session>, std::_Placeholder<1>))(boost::system::error_code)>, boost::asio::strand<boost::asio::io_context::executor_type> >; NextLayer = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; bool deflateSupported = true; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]: Assertion `d.ws.wr_block_ == d.tok' failed.  
  
I can trace this back to the ws.async_ping-command. Everything works fine without it, and in particular I can exchange my messages (without ping/pong) between client and server.   
  
This happens with the Beast-version contained in Boost 1.66 on Ubuntu Linux 17.10 / gcc 7.2 .  
  
For completeness, here are the two functions / calls I believe are relevant:  
  
        [...]  
	m_ws.async_ping(  
		m_ping_data  
		, boost::asio::bind_executor(  
			m_strand  
			, std::bind(  
				&async_websocket_server_session::when_ping_sent  
				, shared_from_this()  
				, std::placeholders::_1  
			)  
		)  
	);  
       [...]  
  
void async_websocket_server_session::when_ping_sent(boost::system::error_code ec) {  
  // something  
}  
  
All I'd need is a pointer in the right direction, so I can better focus my search for the error.  
  
In any case thanks,  
Beet

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-12 17:38:22 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357304957  

In the version which shipped in 1.66.0 it is possible for the control frame callback to execute outside of the strand. You can try one or both of these things, and if the problem goes away then that is a pretty good indication that you are affected by the issue:  
  
1. Use an implicit strand (only one thread calling `io_context::run`)  
  
2. In the control frame callback, instead of executing the work immediately, post a lambda to the strand which executes the work  
  
However, I notice that in your assertion you have `deflateSupported == true`. This means you are using the version of Beast in the master or develop barnches, not Boost 1.66.0. Which version are you using?

---

## Comment 2

> Username: rberlich  
> Created at: 2018-01-12 18:26:19 UTC  
> Updated at: 2018-01-12 18:34:16 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357316591  

Uh, yes, I should have given more information. I have actually tried to use the current Beast-version 151 from Github (I have just moved the Beast-includes to the Boost 1.66 dir), however this hasn't affected the problem. So I see this with both a pristine Boost 1.66 and the Beast 151 headers moved to the Boost 1.66 directory.  
  
I will try to run with a single io_context.run() call. However the problem happens on the first invocation of async_ping, the callback is never reached.  
  
The frame callback is a std::function filled with a lambda (as I had some problems getting lambdas to work with Beast and/or ASIO. For completeness, here is the function:  
  
	 std::function<void(boost::beast::websocket::frame_type, boost::beast::string_view)> f_when_control_frame_arrived  
		 = [this](frame_type frame_t, string_view /* unused */) {  
			 if(  
				 // We might have received a pong as an answer to a our own ping,  
				 // or someone might be sending us pings. In either case the line is active.  
				 boost::beast::websocket::frame_type::pong==frame_t  
				 || boost::beast::websocket::frame_type::ping==frame_t  
			 ) {  
                                // [...]  
  
				 // Note that the connection is alive  
				 this->m_ping_state = ping_state::CONNECTION_IS_ALIVE;  
			 }  
		 };  
  
This is then registered like this inside of the run()-call:  
  
m_ws.control_callback(f_when_control_frame_arrived);

---

## Comment 3

> Username: rberlich  
> Created at: 2018-01-12 19:36:21 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357333693  

O.k., I have now tried both methods. Using just one io_context::run() call doesn't change the problem (with a pristine Boost 1.66), and using boost::asio::post directly doesn't seem to make a difference either. For completeness, here is the complete run()-function including how the call-back is posted (in a rather crude way, I',m afraid):  
  
```  
void async_websocket_server_session::async_start_run() {  
	// --------------------------------------------------------------------------  
	// The ping cycle  
  
	// Set a control-frame callback  
	auto self = shared_from_this();  
	f_when_control_frame_arrived  
		= [self](frame_type frame_t, string_view s) {  
		boost::asio::post(  
			boost::asio::bind_executor(  
				self->m_strand  
				, std::bind(  
					&async_websocket_server_session::on_control_callback  
					, self  
					, frame_t  
					, s  
				)  
			)  
		);  
	};  
  
	// Set the callback to be executed on every incoming control frame.  
	m_ws.control_callback(f_when_control_frame_arrived);  
  
	// Start the first ping  
	async_start_ping();  
  
	// --------------------------------------------------------------------------  
	// Connections and communication  
  
	async_start_accept();  
}  
  
```  
  
The program crashes again at runtime with the assertion `d.ws.wr_block_ == d.tok failed.` in ping.ipp:166 .  
  
`f_when_control_frame_arrived` is a std::function declared like this in the class body:  
  
```  
std::function<void(boost::beast::websocket::frame_type, boost::beast::string_view)> f_when_control_frame_arrived;  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-12 20:05:02 UTC  
> Updated at: 2018-01-12 20:05:34 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357340523  

I need a working, compiling example that reproduces the defect, it is impossible for me to diagnose the problem with what you have provided. Does the advanced-server example have this problem?  
  
Hey why are you doing `async_start_ping` before the websocket handshake? You have to handshake first!

---

## Comment 5

> Username: rberlich  
> Created at: 2018-01-12 20:10:13 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357341655  

O.k., that was it -- thanks!  I keep learning here, and I very much appreciate the work you put into this!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-01-12 20:22:06 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357344167  

\phew...glad it got sorted! I was starting to sweat a little bit

---

## Comment 7

> Username: rberlich  
> Created at: 2018-01-12 20:45:11 UTC  
> Updated at: 2018-01-12 20:50:00 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357349134  

Hey, this is great! I can now let my server serve 10000 objects (each of which again holds 1000 objects with a random number) to 250 clients in less than 29 seconds. This includes (de-)serialization, sorting on the client side and sending the "sorted" objects back to the server. In Debug mode ... . Without a single error, and all of this on a AMD Ryzen with "only" 6 native cores. My goal is actually to scale this to 500 clients, but I'll have to do this on a cluster -- the load on this single machine skyrockets of course, and the time needed will be CPU-bound here. But again -- this is seriously great! Thanks a million!  
  
P.S.: Serialization is done with Boost.Serialization in XML-mode, so using binary serialization will lower the workload. Just working on this.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-01-12 20:57:38 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357351944  

With WebSocket? Awesome!

---

## Comment 9

> Username: rberlich  
> Created at: 2018-01-12 21:13:01 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357355402  

Yep :-)    
  
From measurements with "pure" ASIO code, I actually believe that the limiting factor is probably serialization (with far more complex objects in my "real" use case). In the past, as I have no way to know in advance how much time clients need for their calculations, I have let them disconnect once they had got a new work item and reconnect when the result was ready. This has led to all sorts of problems, and being able to keep the connection open will probably actually increase the speed despite some overhead involved with Websockets, at least for short- to medium-sized workloads.  And for work items calculating minutes to hours the overhead does not make much difference. So I like the more structured approach I get with Websockets in general and with Beast in particular. Plus, I now have a clean way to know, how many workers I have, which helps with timeout calculations.  
  
With your permission, I'll close this thread for now, but would appreciate, if I could ask further questions in this forum if and when they arise.  
  
Again, thanks a lot for your help!

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-01-12 22:12:30 UTC  
> Url: https://github.com/boostorg/beast/issues/978#issuecomment-357368885  

> if I could ask further questions in this forum if and when they arise.  
  
Yep, feel free to open new issues as needed. I'm sure anyone who goes through these closed issues will be likely to find something relevant.
