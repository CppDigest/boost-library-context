# #363 - Broken off network sends since Boost (probably ASIO) 1.74 [Closed]

> Username: audaki  
> Created at: 2020-09-12 13:07:24 UTC  
> Updated at: 2020-12-30 01:14:43 UTC  
> Closed at: 2020-12-30 01:14:42 UTC  
> Url: https://github.com/boostorg/asio/issues/363  

3 Years ago we have developed a small (TLS 1.2+ only) HTTPs server for our web application.  
  
We are using Boost for that since 1.64 (development started May 2017) and we never had a problem. Now I have a problem with Boost 1.74, which is always 100% reproducible. When I compile my application with Boost 1.73 the problem is gone, when I compile it with Boost 1.74 it's back. I have tried this 6 times and even tried again with backported TLS libraries in the meantime (didn't change a thing sadly). It is always reproducible when switching the Boost version and recompiling everything and it is the same problem on 2 different PCs and internet connections. I am 100% sure this is a software bug.  
  
With Boost 1.74 our HTTPs server responses occasionally "stops" at some 4 KiB boundary. And I mean literally stops, the browser thinks it is a succcessful response. Let's say we have a javascript file that is 73 KiB. With all other Boost versions the user *always* receives all 73 KiB.  
  
But with Boost 1.74 in 95% of responses it works. But in about 5% of the responses (and yes, it is that often) the response is cut short. I've seen for example response sizes of exactly 20 KiB, exactly 24 KiB and exactly 40 KiB (I could've probably find more sizes but I had to fix our production service ;-) ). I can easily reproduce the issue by pressing F5 between 5 and 10 times until some resource is broken off.  
  
It is noteworthy that the problem is on exact 4 KiB boundaries!

---

## Comment 1

> Username: audaki  
> Created at: 2020-09-12 13:20:46 UTC  
> Url: https://github.com/boostorg/asio/issues/363#issuecomment-691486731  

PS: Some more info:  
  
- Compiled with clang 10 in C++17 mode  
  
- Running libstdc++ of GCC10 for C++17 feature support  
  
- Running on openSUSE Leap 15.2  
  
- Everything compiled with -O3 and -march=native  
  
The relevant code is this:  
  
```  
    std::string cache;  
  
[...]  
  
    response->body()->sputn(cache.data(), cache.size());  
  
[...]  
  
// and body() is:  
  
  boost::asio::streambuf* body() noexcept  
  {  
    return &body_buffer_;  
  }  
  
[...]  
  
// Class members:  
  
private:  
  boost::asio::streambuf head_buffer_;  
  boost::asio::streambuf body_buffer_;  
  boost::asio::streambuf gzipped_body_buffer_;  
  
  std::multimap<std::string, std::string, std::less<>> headers_;  
  std::vector<boost::asio::streambuf::const_buffers_type> buffers_;  
  
[...]  
  
// Class is getting ready for send:  
  
/**  
 * @brief Response::buildHead  
 */  
void  
Response::buildHead()  
{  
  std::ostream head{&head_buffer_};  
  head << "HTTP/1.1 " << statusCode_.code() << " " << statusCode_.reasonPhrase() << "\r\n";  
  
  //     head << "Connection: keep-alive\r\n";  
  head << "Keep-Alive: timeout=300, max=1000\r\n";  
  
  for (auto& header: headers_) {  
    head << header.first << ": " << header.second << "\r\n";  
  }  
  
  
  
  if (use_gzip_ && (head_buffer_.size() + body_buffer_.size()) < 1000) {  
    use_gzip_ = false;  
  }  
  
  if (use_gzip_) {  
    boost::iostreams::filtering_streambuf<boost::iostreams::input> in;  
    in.push(boost::iostreams::gzip_compressor(4));  
    in.push(body_buffer_);  
    boost::iostreams::copy(in, gzipped_body_buffer_);  
    head << "Content-Length: " << gzipped_body_buffer_.size() << "\r\n";  
    head << "Content-Encoding: gzip\r\n";  
  } else {  
    head << "Content-Length: " << body_buffer_.size() << "\r\n";  
  }  
  
  head << "\r\n";  
}  
  
/**  
 * @brief Response::getBuffersReadyForSending  
 * @return -  
 */  
std::vector<boost::asio::streambuf::const_buffers_type>&  
Response::getBuffersReadyForSending()  
{  
  if (!hasHead()) {  
    buildHead();  
  }  
  
  buffers_.push_back(head_buffer_.data());  
  
  if (use_gzip_) {  
    buffers_.push_back(gzipped_body_buffer_.data());  
  } else {  
    buffers_.push_back(body_buffer_.data());  
  }  
  
  return buffers_;  
}  
  
[...]  
  
// Response writer:  
  
  connection_.asyncWrite(response_->getBuffersReadyForSending());  
  
[...]  
  
// In connection class   
  
  template<typename ConstBufferSequence>  
  void asyncWrite(const ConstBufferSequence& buffers)  
  {  
  
    if (ssl_stream_.lowest_layer().is_open())  
      ssl_stream_.lowest_layer().set_option(boost::asio::ip::tcp::no_delay{false});  
  
    std::size_t total_bytes = boost::asio::buffer_size(buffers);  
  
    auto handler = strand_.wrap(std::bind(&Connection::onWrite, shared_from_this(), std::placeholders::_1, std::placeholders::_2));  
    boost::asio::async_write(ssl_stream_, buffers, handler);  
  
    if (total_bytes < 64 * 1024 && ssl_stream_.lowest_layer().is_open())  
      ssl_stream_.lowest_layer().set_option(boost::asio::ip::tcp::no_delay{true});  
  }  
  
  
[...]  
  
// ssl_stream definition:  
  
  boost::asio::ssl::stream<boost::asio::ip::tcp::socket> ssl_stream_;  
```  
  
  
Could the problem be related to a different handling of TCP no delay? I think though the problem hit resources bigger and smaller than 64 KiB.

---

## Comment 2

> Username: audaki  
> Created at: 2020-09-12 16:37:45 UTC  
> Updated at: 2020-09-12 17:38:22 UTC  
> Url: https://github.com/boostorg/asio/issues/363#issuecomment-691514244  

I have added some debug logging and I know what's happening (but not the cause for it):  
  
2020-09-12 18:35:28 asyncWrite (Connection 1) 210999 bytes  
2020-09-12 18:35:28 onWrite (Connection 1) 32 errorCode ('Broken pipe'); 24576 bytes written  
  
It seems like with Boost 1.74 (and only with Boost 1.74) the pipes get broken. And always exactly at 4 KiB boundaries.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 01:14:41 UTC  
> Url: https://github.com/boostorg/asio/issues/363#issuecomment-752293712  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#766](https://github.com/chriskohlhoff/asio/issues/766).
