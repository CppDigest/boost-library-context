# #2131 - crashes during http::async_write [Closed]

> Username: JindrichD  
> Created at: 2020-12-02 13:56:16 UTC  
> Updated at: 2020-12-03 11:12:52 UTC  
> Closed at: 2020-12-03 11:12:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2131  

hi,  
i want to write http client able to do http pipelining (which requires calling write() independently on calling read()) but ended with randomly crashing program - usually after several thousands requests.  
i reused http_client_async.cpp from boost example (which btw. worked well for request-response) and modified it so that i added queue which is used for buffering http requests - this queue is read by thread which then calls http::async_write(). so async_write is called as quickly as possible for incoming requests. on_write handler does nothing.  
then there is another thread which called http::async_read() and in on_read handler i process the response and call http::async_read again.  
this seems to work for few requests, however if i let the program running, im getting random crashes in  
`#4  0x00000000005ec200 in to_value_ptr (n=@0x7fdd819739d0: 0x0) at boost/1.74.0/include/boost/intrusive/detail/hook_traits.hpp:62`  
'this' is not always 0x0, but usually some nonsense number like 0x86 or so  
and another crash occurs from times to time here  
`#0  0x00000000005efc67 in boost::beast::http::basic_fields<std::allocator<char> >::value_type::buffer (this=0x260) at boost/1.74.0/include/boost/beast/http/impl/fields.hpp:287`  
  
so i wanted to ask how this can be achieved, because it seems that when the functions are called like async_write->on_write->from here call async_read()->on read()->call async_write() it works. but at the moment i call asyn_write and async_read independently, the program beast has a problem with it.  
  
btw. my BOOST_BEAST_VERSION is 300  
  
thanks for help!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-12-02 14:17:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2131#issuecomment-737257177  

Performing asynchronous operations (i.e. read and write) concurrently on the same stream from different threads will result in Undefined Behaviour.  
  
All access to any asio stream (including via beast) must take place on the same _logical strand_.  
  
This would normally be acheived by having your stream controlled by an asio::strand<executor_type>.  
  
Your worker threads will need to post() work to the strand in order to initiate read and write requests, which will complete on the strand. The handlers of these reads and writes will then post work to your worker threads.

---

## Comment 2

> Username: JindrichD  
> Created at: 2020-12-02 14:58:24 UTC  
> Updated at: 2020-12-02 15:06:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2131#issuecomment-737282976  

Ok. I have the stream in strand, or at least this is what i have there   
`beast::tcp_stream stream_;   
stream_(net::make_strand(ioc))`  
similarly as is in the examples.  
but i don't fully understand how shall i achive   
> Your worker threads will need to post() work to the strand in order to initiate read and write requests  
  
the code is as following  
  
```  
boost::asio::io_context ioc;  
tcp::resolver resolver_(net::make_strand(ioc)  
beast::tcp_stream stream_{net::make_strand(ioc)};  
beast::flat_buffer buffer_;  
Queue request_queue_;  
```  
  
one thread starts the connection:  
  
```  
void run() {  
  resolver_.async_resolve(...);  
}  
void on_resolve(...) {  
  stream_.async_connect(...);  
}  
void on_connect(...) {  
  http::async_read(stream_, buffer_, *res_, on_read);  
}  
void on_read(...) {  
   //handle the response and continue reading  
  ...  
  http::async_read(stream_, buffer_, *res_, on_read);  
}  
```  
  
and another thread is reading the queue and doing write  
  
```  
std::thread t = std::thread(&session2::processQ, this);  
void processQ() {  
  while(running_) {  
     //read from queue and write the req  
     ....  
    http::async_write(stream_, req, on_write);  
 }  
}  
void on_write(...) {  
   //does nothing much  
}  
  
```  
so i cant figure out how did you mean to post work to strand while i'm calling boost::beast::http::async_write which handles the stream itself.  
  
thanks!

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-12-02 19:11:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2131#issuecomment-737436844  

> one thread starts the connection:  
> and another thread is reading the queue and doing write  
That's your problem. The two threads are touching the stream with no mutual exclusion. This is not allowed.  
  
The reader and writer orthogonal regions must execute on the same strand.  
Note that the strand provided to the stream at construction is merely the _default executor_ . It determines which executor the completion handler will run on if no executor is bound the the completion handler (callback) you supply.  
It does _not_ protect the strand from two threads simultaneously initiating an asyncronous operation which is what is happening in your program.  
Methods on streams' mutable interfaces _are not thread safe_.

---

## Comment 4

> Username: JindrichD  
> Created at: 2020-12-03 11:12:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2131#issuecomment-737874907  

Hi, so after some more reading and trying, i managed to figure it out. so thanks for pointing me to the right direction.
