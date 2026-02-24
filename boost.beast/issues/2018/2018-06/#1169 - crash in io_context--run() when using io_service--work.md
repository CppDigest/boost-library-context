# #1169 - crash in io_context::run() when using io_service::work [Closed]

> Username: mirkub  
> Created at: 2018-06-22 10:44:13 UTC  
> Updated at: 2018-07-26 11:26:08 UTC  
> Closed at: 2018-07-26 11:26:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1169  

Hello, I'm seeing strange crashes when using boost::asio::io_service::work with the io_context. Please have a look at gdb.txt. It doesn't give much clue for debugging for me.  
  
### Version of Beast  
167  
  
### Steps necessary to reproduce the problem  
Start asynchronous http client (see attached).  
  
### All relevant compiler information  
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-11)  
  
[gdb.txt](https://github.com/boostorg/beast/files/2127285/gdb.txt)  
[client.cpp.txt](https://github.com/boostorg/beast/files/2127310/client.cpp.txt)  
[client.h.txt](https://github.com/boostorg/beast/files/2127311/client.h.txt)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-22 14:27:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1169#issuecomment-399461405  

* Although a `deque` does not invalidate iterators on insertions, it is not thread safe. You are invoking non-const members on the `deque` concurrently.  
  
* Your code tries to do more than one `async_read` and more than one `async_write` at a time. You can't do more than one. The code needs to wait until the read or write completes before issuing a new one.

---

## Comment 2

> Username: mirkub  
> Created at: 2018-06-22 14:43:58 UTC  
> Updated at: 2018-06-22 15:11:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1169#issuecomment-399466827  

Can we ignore the code weaknesses of thread safety? It crashes regularly only when the io_service::work is involved (after very 1st call to add_request_to_queue()).  
  
The call flow is like this:  
ENTER [method=Client::Client(const string&, int)]  
  ENTER [method=session::session(boost::asio::io_context&, const string&)]  
  LEAVE [method=session::session(boost::asio::io_context&, const string&)]  
  ENTER [method=void session::async_resolve(int)]  
  LEAVE [method=void session::async_resolve(int)]  
LEAVE [method=Client::Client(const string&, int)]  
ENTER [method=void session::on_resolve(boost::system::error_code, boost::asio::ip::basic_resolver<boost::asio::ip::tcp>::results_type)]  
LEAVE [method=void session::on_resolve(boost::system::error_code, boost::asio::ip::basic_resolver<boost::asio::ip::tcp>::results_type)]  
ENTER [method=void session::on_connect(boost::system::error_code)]  
LEAVE [method=void session::on_connect(boost::system::error_code)]  
ENTER [method=void Client::doAsyncGet(const string&, ResponseHandler)]  
  ENTER [method=void session::add_request_to_queue(const string&, ResponseHandler)]  
  LEAVE [method=void session::add_request_to_queue(const string&, ResponseHandler)]  
LEAVE [method=void Client::doAsyncGet(const string&, ams::ResponseHandler)]  
***CRASH***  
  
Does the gdb.txt gives any clue?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-22 15:15:40 UTC  
> Updated at: 2018-06-22 15:15:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1169#issuecomment-399477040  

This is from gdb.txt:  
```  
#0  buffer (this=0x0) at /data/git/ams-boost/include/boost/beast/http/impl/fields.ipp:337  
#1  operator* (this=<synthetic pointer>) at /data/git/ams-boost/include/boost/beast/http/impl/fields.ipp:80  
#2  buffer_size<boost::beast::http::basic_fields<std::allocator<char> >::writer::field_iterator> (end=..., begin=...) at /data/git/ams-boost/include/boost/asio/buffer.hpp:553  
#3  buffer_size<boost::beast::http::basic_fields<std::allocator<char> >::writer::field_range> (b=...) at /data/git/ams-boost/include/boost/asio/buffer.hpp:586  
```  
  
Either the message is being destroyed while being sent, or two threads are accessing a non-thread-safe object concurrently. Both of these issues are symptoms of the problems I described regarding your code and issuing multiple reads and writes at once.

---

## Comment 4

> Username: mirkub  
> Created at: 2018-06-25 12:35:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1169#issuecomment-399936417  

Hello Vinnie,  
I'm still struggling with the crash. I have modified your example to reproduce the issue. Can you run it in your your env?  
Thanks,  
Miro  
[client.cpp.txt](https://github.com/boostorg/beast/files/2133076/client.cpp.txt)

---

## Comment 5

> Username: mirkub  
> Created at: 2018-06-25 13:04:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1169#issuecomment-399944515  

I think I have found the problem: http::async_write() takes REFERENCE as the 2nd parameter which goes out of scope.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-06-25 14:46:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1169#issuecomment-399977823  

Yes, I see it now:  
  
```  
		queue_item q;  
		q.req_.version(11);  
		q.req_.method(http::verb::get);  
		q.req_.target(target);  
		q.req_.set(http::field::host, m_host);  
		q.req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
		q.handler_=responseHandler;  
		m_sendingQueue.push_back(q);  
  
		http::async_write(socket_, q.req_,  
				std::bind(  
					&session::on_write,  
					shared_from_this(),  
					std::placeholders::_1,  
					std::placeholders::_2));  
```

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-07-25 15:03:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1169#issuecomment-407786723  

This issue has been open for a while with no activity, has it been resolved?
