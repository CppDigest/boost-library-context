# #2281 - How to avoid race condition while multiple threads had to write to the same (WS) session? [Closed]

> Username: vtharmalingam  
> Created at: 2021-07-07 19:04:26 UTC  
> Updated at: 2021-07-09 17:23:46 UTC  
> Closed at: 2021-07-09 17:23:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2281  

Note:  
I had written a personal email to Vinnie, and he advised me to open an issue here.  
  
I am trying to experiment the "beast" powered websocket for my server app. I was fairly successful in putting the pieces together. And I followed the same pattern of your famous "beast" websocket example.  
  
But I have a classical multi-threaded problem as explained below:  
  
**state**: This object track the list of all websocket sessions  
**websocket**: objects of all open sessions  
  
For each random interval, 3-4 threads in "state" push information down the sessions. And oftentimes, the same session will receive the "send" requests through multiple threads. And the messages when received in the WebSocket class get queued in std::vector.   
  
Here is the problem: Within the "on_write" function the race condition occurs (thanks to your advice a few days back!) and causes the access violation error. I am a data scientist, I have not had an opportunity to do much of C++/MT work, so am struggling my head around this issue for several days. Tried solutions using mutex/lock_guard  within the send function, but it is not always working as expected.  
  
Any direction will be really appreciated. Thank you for your help, in advance.  
  
Thanks,  
Tharma  
  
```cpp  
  
// ==============================  
// shared_state.hpp  
// ==============================  
  
// Forward declaration  
class websocket_session;   
  
// Represents the shared server state  
class shared_state : public std::enable_shared_from_this<shared_state>  {  
	  
	//ZMQ  
	ZMQPairSub *zmqSub;  
  
	  
	//secondary store  
	std::unordered_set<websocket_session*> sessions_;  
  
	  
public:  
	explicit shared_state(); //new  
  
	//for add user  
	void join(long int login, websocket_session& session);  
	void leave(websocket_session& session);  
  
	  
	std::unordered_set<websocket_session*> sessions(); // tracking all websocket clients  
	int check_if_exists(websocket_session& ses);  
  
	//Thread Method  
	void zmq_sub_listener();  
	void push_client_data();  
  
	void send_session_data(websocket_session* session, std::string& s);  
	   
};  
```  
  
```cpp  
  
// ==============================  
// shared_state.cpp  
// ==============================  
  
shared_state::shared_state()  
{   
	//[0]. Listening "Sink" subscription events  
	auto b0 = boost::bind(&shared_state::zmq_sub_listener, this);  
	boost::thread zmq_sub_listener_thread(b0);  
	zmq_sub_listener_thread.detach();  
  
	//[1]. Periodic Data Check and Data Push (to Authenticated Clients)  
	auto b = boost::bind(&shared_state::push_client_data, this);  
	boost::thread tc_thread(b);  
	tc_thread.detach();  
}  
  
//All Connections  
void shared_state::join(long int login, websocket_session& session)  
{  
	//set - struct  
	sessions_.insert(&session);  
}  
  
  
//This method manipulated both the state members that work as a store of sessions  
void shared_state::leave(websocket_session& session)  
{  
	//set - struct  
	sessions_.erase(&session);  
}  
   
std::unordered_set<websocket_session*> shared_state::sessions()  
{  
	return sessions_;  
}  
  
  
// Thread method  
void shared_state::zmq_sub_listener()  
{  
	while (true)  
	{  
		try  
		{  
			//hard-coded  
			std::string icoming_login = 292929929;  
  
			for (auto session : sessions_) {  
		  
				if ( session->login == 292929929 ){ 				  
						auto const out_ = std::make_shared<std::string const>(random);  
						session(out_);  
						  
				}  
		  
			}  
		  
		}  
		   
		catch (std::exception& e)  
		{  
			std::cout << e.what() << std::endl;  
		}  
  
  
		boost::this_thread::sleep_for(boost::chrono::seconds(15));  
		   
	} //while-loop	  
}  
  
 // Thread method 2  
void shared_state::push_client_data()  
{  
	while (true)  
	{  
		try  
		{  
			for (auto& session : sessions_)  
			{  
		  
				std::string msg = getCustomerData(session->login);  
		  
		  
				//push data out to socket  
				auto const out_ = std::make_shared<std::string const>(msg);  
				session->send(out_);  
				  
			}  
		}  
		  
  
		catch (std::exception& e)  
		{  
			std::cout << e.what() << std::endl;  
		}  
  
		//15 seconds  
		boost::this_thread::sleep_for(boost::chrono::seconds(15));  
	} //while-loop	  
}  
    
//sending a given message to all sessions  
void shared_state::send(std::string message)  
{  
	const auto ss = std::make_shared<const std::string>(std::move(message));  
	for (auto session : sessions_)  
	{  
		session->send(ss);  
	}  
}   
  
```  
  
  
```cpp  
  
// ==============================  
// websocket_session.hpp  
// ==============================  
  
// Forward declaration  
class shared_state;  
  
class websocket_session : public std::enable_shared_from_this<websocket_session>  
{  
	beast::flat_buffer buffer_;  
	websocket::stream<beast::ssl_stream<beast::tcp_stream>> ws_;  
  
	std::shared_ptr<shared_state> state_;  
  
	//container  
	std::vector<std::shared_ptr<const std::string>> queue_;  
  
	void fail(beast::error_code ec, const char* what);  
	void on_accept(beast::error_code ec);  
	void on_read(beast::error_code ec, std::size_t bytes_transferred);  
	void on_write(beast::error_code ec, std::size_t bytes_transferred);  
  
	void queuePush(const std::shared_ptr<const std::string>& s);  
	std::string queuePop(bool erase);  
		  
	// a global instance of std::mutex to protect global variable  
	std::mutex myQueueMutex;  
  
  
public:  
	websocket_session(  
		beast::ssl_stream<beast::tcp_stream> stream  
		, const std::shared_ptr<shared_state>& state)  
	);  
  
	~websocket_session();  
  
	template <class Body, class Allocator>  
	void  
	run(http::request<Body, http::basic_fields<Allocator>> req)  
	{  
		// Accept the WebSocket upgrade request  
		do_accept(std::move(req));  
	}  
  
  
	template <class Body, class Allocator>  
	void  
	do_accept(http::request<Body, http::basic_fields<Allocator>> req)  
	{  
		// Set suggested timeout settings for the websocket  
		ws_.set_option(  
			websocket::stream_base::timeout::suggested(  
				beast::role_type::server));  
  
		// Set a decorator to change the Server of the handshake  
		ws_.set_option(  
			websocket::stream_base::decorator(  
				[](websocket::response_type& res)  
				{  
					res.set(http::field::server,  
					        std::string(BOOST_BEAST_VERSION_STRING) +  
					        " wt-server-flex");  
				}));  
  
		// Accept the websocket handshake  
		ws_.async_accept(  
			req,  
			beast::bind_front_handler(  
				&websocket_session::on_accept,  
				shared_from_this()));  
	}   
  
	//void do_accept();  
	void  
	do_read();  
  
	// Send a message  
	void send(const std::shared_ptr<const std::string>& ss);  
  
};  
  
  
```  
  
  
  
```cpp  
  
// ==============================  
// websocket_session.cpp  
// ==============================  
  
void   
websocket_session::queuePush(const std::shared_ptr<const std::string>& s)  
{  
      // added mutex... good?  
	std::lock_guard<std::mutex> guard(myQueueMutex);  
	queue_.push_back(s);	  
	  
}  
  
std::string   
websocket_session::queuePop(bool erase)  
{  
	std::lock_guard<std::mutex> guard(myQueueMutex);  
	if (erase)  
		queue_.erase(queue_.begin());  
  
	if (!queue_.empty())  
		return *queue_.front();  
	else  
		return "";  
}  
  
  
void websocket_session::send(const std::shared_ptr<const std::string>& ss)  
{  
	// Always add to queue  
	//queue_.push_back(ss);  
	queuePush(ss);  
	  
	// Are we already writing?  
	if (queue_.size() > 1)  
		return;  
  
	std::string d = queuePop(FALSE);  
	 	  
	// We are not currently writing, so send this immediately  
	ws_.async_write(  
		net::buffer(d),  
		[sp = shared_from_this()](  
		beast::error_code ec, std::size_t bytes)  
		{  
			sp->on_write(ec, bytes);  
		});  
}  
  
//This gets called recursively...  
void websocket_session::on_write(beast::error_code ec, std::size_t)  
{		  
	// Handle the error, if any  
	if (ec)  
	{  
		if ((ec.category() == boost::asio::error::get_ssl_category())  
			&& (SSL_R_PROTOCOL_IS_SHUTDOWN == ERR_GET_REASON(ec.value())))  
		{  
			// Close the tcp_stream  
			get_lowest_layer(ws_).close();  
			state_->leave(*this); //log the user out						  
		}  
  
		return fail(ec, "write");  
	}  
	  
	std::string d = queuePop(TRUE);  
  
	if (d.empty())  
		return;  
   
	// Send the next message if any  
	if (!queue_.empty())  
		ws_.async_write(  
			net::buffer(d),  
			[sp = shared_from_this()](  
				beast::error_code ec, std::size_t bytes)  
	{  
		sp->on_write(ec, bytes);                      //Note: recursive call  
	});  
	   
}  
  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-07-07 19:08:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2281#issuecomment-875859046  

You can't "compose" operations using `queuePush` and `queuePop` because the mutex has to be held for the duration of the calling function. Specifically here:  
```  
	// Always add to queue  
	//queue_.push_back(ss);  
	queuePush(ss);  
	  
	// Are we already writing?  
	if (queue_.size() > 1)  
		return;  
  
	std::string d = queuePop(FALSE);  
	 	  
```  
  
you are checking `size()` outside the mutex. You need to protect the body of `send` with the mutex.

---

## Comment 2

> Username: vtharmalingam  
> Created at: 2021-07-08 06:22:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2281#issuecomment-876163955  

Thank you, Vinnie. Let me try it out.   
Really appreciated.  
  
Regards,  
Tharma

---

## Comment 3

> Username: vtharmalingam  
> Created at: 2021-07-09 17:23:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2281#issuecomment-877340099  

Thanks a ton, @vinniefalco, It works as expected.
