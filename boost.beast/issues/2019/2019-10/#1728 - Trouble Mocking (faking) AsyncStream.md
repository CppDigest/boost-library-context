# #1728 - Trouble Mocking (faking) AsyncStream [Closed]

> Username: smccauliff  
> Created at: 2019-10-09 14:05:22 UTC  
> Updated at: 2019-11-28 23:58:41 UTC  
> Closed at: 2019-11-28 23:58:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1728  

I'm trying to mock a stream for testing my session class.  When I do so I get a very long compiler error which eventually complains about an implicitly-deleted copy constructor of boost::beast::http::detail::write_some_op.  The deepest level of compiler error is included below, the stack is very long so I've omitted the other lines in the stack.  When I replace FakeStream with boost::beast::tcp_stream the program compiles. Since there are various asserts on Handler types and Stream types it looks like the method signatures for FakeStream are correct.  Is there something else I'm missing?  
  
Any help you can provide would be useful.  
  
boost beast version 248  
  
/usr/bin/clang++-7 --version  
clang version 7.0.0-3~ubuntu0.18.04.1 (tags/RELEASE_700/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/bits/std_function.h:173:10: error: call to implicitly-deleted copy constructor of 'boost::beast::http::detail::write_some_op<boost::beast::http::detail::write_op<boost::beast::http::detail::write_msg_op<std::_Bind<void (Session<boost::beast::http::basic_dynamic_body<boost::beast::buffers_adaptor<std::vector<boost::asio::mutable_buffers_1, std::allocator<boost::asio::mutable_buffers_1> > > >, FakeStream>::*(std::shared_ptr<Session<boost::beast::http::basic_dynamic_body<boost::beast::buffers_adaptor<std::vector<boost::asio::mutable_buffers_1, std::allocator<boost::asio::mutable_buffers_1> > > >, FakeStream> >, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, unsigned long)>, FakeStream, true, boost::beast::http::basic_dynamic_body<boost::beast::buffers_adaptor<std::vector<boost::asio::mutable_buffers_1, std::allocator<boost::asio::mutable_buffers_1> > > >, boost::beast::http::basic_fields<std::allocator<char> > >, FakeStream, boost::beast::http::detail::serializer_is_done, true, boost::beast::http::basic_dynamic_body<boost::beast::buffers_adaptor<std::vector<boost::asio::mutable_buffers_1, std::allocator<boost::asio::mutable_buffers_1> > > >, boost::beast::http::basic_fields<std::allocator<char> > >, FakeStream, true, boost::beast::http::basic_dynamic_body<boost::beast::buffers_adaptor<std::vector<boost::asio::mutable_buffers_1, std::allocator<boost::asio::mutable_buffers_1> > > >, boost::beast::http::basic_fields<std::allocator<char> > >'  
            new _Functor(*__source._M_access<_Functor*>());  
  
```c++  
#include <memory>  
#include <string>  
#include <vector>  
#include <iostream>  
  
#include <boost/asio/strand.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
  
namespace http = boost::beast::http;  // from <boost/beast/http.hpp>  
namespace net = boost::asio;          // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;     // from <boost/asio/ip/tcp.hpp>  
  
struct FakeStream {  
  using results_type = tcp::resolver::results_type;  
  
  using endpoint_type = tcp::endpoint;  
  
  using executor_type = boost::asio::strand<boost::asio::io_context::executor_type>;  
  
  struct State {  
    executor_type executor;  
    explicit State(const executor_type& executor) : executor{executor} {}  
  };  
  
  std::shared_ptr<State> state_;  
  explicit FakeStream(const executor_type& executor) {  
    state_ = std::make_shared<State>(executor);  
  }  
  
//  FakeStream(const FakeStream& other) = default;  
//  FakeStream& operator=(const FakeStream& other) = default;  
//  
//  FakeStream(FakeStream&& other) = delete;  
//  FakeStream& operator=(FakeStream&& other) = delete;  
//  ~FakeStream() = default;  
  
  void expires_after(std::chrono::seconds expiration_time) {}  
  
  using read_write_handler_t = std::function<void(boost::system::error_code, size_t)>;  
  // https://www.boost.org/doc/libs/1_71_0/doc/html/boost_asio/reference/AsyncReadStream.html  
  template<typename MutableBufferSequence>  
  void async_read_some(MutableBufferSequence const& mutable_buffer,  
                       read_write_handler_t handler) {  
    async_read_called_ = true;  
    boost::system::error_code ec;  
    boost::asio::post(  
      boost::asio::bind_executor(get_executor(), std::bind(std::forward<read_write_handler_t>(handler), ec, 0))  
    );  
  }  
  
  template<typename ConstBufferSequence>  
  void async_write_some(ConstBufferSequence const & const_buffer,  
                        read_write_handler_t&& handler) {  
    async_write_called_ = true;  
    boost::system::error_code ec;  
    size_t bytes_in_buffer = boost::beast::buffer_bytes(const_buffer);  
    boost::asio::post(  
      boost::asio::bind_executor(get_executor(), std::bind(std::forward<read_write_handler_t>(handler), ec, bytes_in_buffer))  
      );  
  }  
  
  static bool async_connected_called_;  
  static bool async_handshake_called_;  
  static bool async_write_called_;  
  static bool async_read_called_;  
  
  static void ClearAllCalledFlags() {  
    async_connected_called_ = false;  
    async_handshake_called_ = false;  
    async_write_called_ = false;  
    async_read_called_ = false;  
  }  
  
  executor_type get_executor() {  
    return state_->executor;  
  }  
  
};  
  
bool FakeStream::async_connected_called_ = false;  
bool FakeStream::async_handshake_called_ = false;  
bool FakeStream::async_read_called_ = false;  
bool FakeStream::async_write_called_ = false;  
  
template<typename Body, typename AsyncStream>  
struct Session : std::enable_shared_from_this<Session<Body, AsyncStream>> {  
  
  std::shared_ptr<AsyncStream> stream_;  
  std::shared_ptr<http::request<Body>> request_;  
  
  Session(net::io_context& io_context) :  
    stream_{std::make_shared<AsyncStream>(net::make_strand(io_context))} {  
  }  
  
  void InitiateWrite(std::shared_ptr<http::request<Body>> request) {  
  
    request_ = request;  
    http::async_write(*stream_, *request_, std::bind(  
      &Session::OnWrite,  
      this->shared_from_this(),  
      std::placeholders::_1,  
      std::placeholders::_2  
    ));  
  }  
  
  void OnWrite(boost::system::error_code ec, std::size_t bytes_transferred) {  
    std::cout << "OnWrite called." << std::endl;  
  }  
  
};  
  
int main(int argc, char** argv) {  
  net::io_context io_context;  
  
  std::string a = "abc";  
  std::string b = "wzyz";  
  auto buffer0 = boost::asio::buffer(a);  
  auto buffer1 = boost::asio::buffer(b);  
  std::vector buffers{buffer0, buffer1};  
  auto buffer_adaptor = boost::beast::buffers_adaptor(buffers);  
  buffer_adaptor.prepare(buffer0.size() + buffer1.size());  
  buffer_adaptor.commit(buffer0.size() + buffer1.size());  
  using body_type = boost::beast::http::basic_dynamic_body<decltype(buffer_adaptor)>;  
  using request_type = boost::beast::http::request<body_type>;  
  using header_type = request_type::header_type;  
  auto request = std::make_shared<request_type>(header_type(), buffer_adaptor);  
  //works: auto session = std::make_shared<Session<body_type, boost::beast::tcp_stream>>(io_context);  
  auto session = std::make_shared<Session<body_type, FakeStream>>(io_context);  
  session->InitiateWrite(request);  
  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-09 14:07:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-540017404  

`std::function` cannot capture move-only types. Try using this mock stream instead:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/_experimental/test/stream.hpp#L49

---

## Comment 2

> Username: smccauliff  
> Created at: 2019-10-09 17:25:14 UTC  
> Updated at: 2019-10-09 17:25:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-540103134  

I looked at the experimental stream testing class.  It seems useful,  but I would need to modify it in order to do what I want to do.  Without some understanding of what I'm doing wrong I'm afraid I would end up in the same spot.  
  
The program still fails to compile with the same error when these static_asserts are added (meaning these static asserts succeed).  So FakeStream seems to be copyable.   
```c++  
  static_assert(std::is_copy_assignable<FakeStream>::value);  
  static_assert(std::is_copy_constructible<FakeStream>::value);  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-09 17:29:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-540105164  

FakeStream needs to support move-only handlers, so you can't use `std::function`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-10-09 17:30:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-540105697  

> I would need to modify it in order to do what I want to do  
  
What do you need it to do?

---

## Comment 5

> Username: smccauliff  
> Created at: 2019-10-22 21:11:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-545157423  

Thanks for the quick reply.  Move-only handlers was the thing that I was missing.  
  
I don't remember all the compiler errors I got when using the fake stream in experimental, but I believe it does not use strand or have an executor type parameter. It could probably be make to work with more effort.

---

## Comment 6

> Username: smccauliff  
> Created at: 2019-10-22 21:27:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-545163468  

Is there a good place to ask general asio questions?  Stackoverflow?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-10-22 21:33:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-545165556  

That's a good place, you can try the C++ Slack, email me for a link.

---

## Comment 8

> Username: stale[bot]  
> Created at: 2019-11-21 22:17:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-557297951  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created at: 2019-11-28 23:58:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1728#issuecomment-559621257  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
