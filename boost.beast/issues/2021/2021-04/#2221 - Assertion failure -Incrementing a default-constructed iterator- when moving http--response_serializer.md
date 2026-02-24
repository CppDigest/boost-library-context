# #2221 - Assertion failure "Incrementing a default-constructed iterator" when moving http::response_serializer [Closed]

> Username: ednolan  
> Created at: 2021-04-24 19:17:37 UTC  
> Updated at: 2024-02-09 20:02:59 UTC  
> Closed at: 2024-02-09 20:02:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2221  

### Version of Beast  
  
315  
  
(The boost version I used was 1.76.0)  
  
### Steps necessary to reproduce the problem  
  
This program reproduces the issue:  
  
```  
#include <boost/beast/http/serializer.hpp>  
#include <boost/beast/http/message.hpp>  
#include <boost/beast/http/string_body.hpp>  
#include <boost/beast/http/status.hpp>  
#include <boost/beast/http/write.hpp>  
#include <boost/asio/io_context.hpp>  
  
#include <algorithm>  
#include <cstddef>  
#include <functional>  
#include <iterator>  
#include <vector>  
#include <boost/asio/associated_allocator.hpp>  
#include <boost/asio/associated_executor.hpp>  
#include <boost/asio/async_result.hpp>  
#include <boost/asio/buffers_iterator.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/system/error_code.hpp>  
  
namespace mock {  
  
template<typename Executor>  
struct basic_async_write_stream {  
  using executor_type = Executor;  
  explicit basic_async_write_stream(executor_type ex) noexcept : ex_(ex) {}  
  auto get_executor() noexcept {  
    return ex_;  
  }  
  using buffer_type = std::vector<std::byte>;  
  const buffer_type& buffer() const noexcept {  
    return buffer_;  
  }  
  buffer_type& buffer() noexcept {  
    return buffer_;  
  }  
  template<typename ConstBufferSequence, typename CompletionToken>  
  auto async_write_some(ConstBufferSequence cb, CompletionToken&& token) {  
    using signature_type = void(boost::system::error_code, std::size_t);  
    auto i = [this](auto h, auto cb) {  
      using iterator_type = boost::asio::buffers_iterator<decltype(cb), std::byte>;  
      auto before = buffer_.size();  
      std::copy(iterator_type::begin(cb), iterator_type::end(cb), std::  
        back_inserter(buffer_));  
      auto ex = boost::asio::get_associated_executor(h, ex_);  
      auto alloc = boost::asio::get_associated_allocator(h);  
      auto c = [h = std::move(h), bytes_transferred = buffer_.size() - before]()  
        mutable { std::invoke(std::move(h), boost::system::error_code(),  
        bytes_transferred); };  
      ex.post(std::move(c), alloc);  
    };  
    return boost::asio::async_initiate<CompletionToken, signature_type>(std::move(i),  
      token, std::move(cb));  
  }  
private:  
  executor_type ex_;  
  buffer_type buffer_;  
};  
  
using async_write_stream = basic_async_write_stream<boost::asio::io_context::  
  executor_type>;  
  
}  
  
int main(int, char**) {  
  boost::beast::http::response<boost::beast::http::string_body> response{boost::beast::http::status::ok, 11};  
  boost::beast::http::response_serializer<boost::beast::http::string_body> serializer{response};  
  boost::asio::io_context io_context{};  
  mock::async_write_stream write_stream{io_context.get_executor()};  
  boost::beast::http::async_write_header(  
      write_stream, serializer,  
      [](boost::system::error_code, std::size_t) {  
      });  
  io_context.run();  
  boost::beast::http::response_serializer<boost::beast::http::string_body> serializer2{std::move(serializer)};  
}  
```  
  
The output of the program is:  
  
```  
a.out: /home/enolan/beast/include/boost/beast/core/impl/buffers_cat.hpp:209: void boost::beast::buffers_cat_view<Buffers>::const_iterator::increment::operator()(boost::mp11::mp_size_t<0>) [with Bn = {boost::asio::const_buffer, boost::asio::const_buffer, boost::asio::const_buffer, boost::beast::http::basic_fields<std::allocator<char> >::writer::field_range, boost::beast::http::chunk_crlf}; boost::mp11::mp_size_t<0> = std::integral_constant<long unsigned int, 0>]: Assertion `(false)&&("Incrementing a default-constructed iterator")' failed.  
Aborted (core dumped)  
```  
  
The assertion failure happens inside the move constructor of `serializer2`.  
  
### All relevant compiler information  
  
Compiled on Ubuntu 21.04 with GCC 10.3 using the following command line:  
  
```  
g++ -std=c++17 -I$HOME/beast/include -I$HOME/boost_1_76_0 ./main.cpp -lpthread  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-04-24 20:08:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-826146875  

Are you sure your mock stream stream is correct? Have you tried it with a simple asio composed write?

---

## Comment 2

> Username: ednolan  
> Created at: 2021-04-26 14:45:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-826893800  

Here is an alternative reproduction without the mock async write stream, using posix::stream_descriptor. It writes to `/tmp/beastissue2221.out` instead of the mock stream's internal buffer. The issue still occurs.  
```  
#include <boost/beast/http/serializer.hpp>  
#include <boost/beast/http/message.hpp>  
#include <boost/beast/http/string_body.hpp>  
#include <boost/beast/http/status.hpp>  
#include <boost/beast/http/write.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/posix/stream_descriptor.hpp>  
#include <cstdio>  
#include <stdio.h>  
  
int main(int, char**) {  
  boost::beast::http::response<boost::beast::http::string_body> response{boost::beast::http::status::ok, 11};  
  boost::beast::http::response_serializer<boost::beast::http::string_body> serializer{response};  
  boost::asio::io_context io_context{};  
  std::FILE* file{std::fopen("/tmp/beastissue2221.out", "w")};  
  boost::asio::posix::stream_descriptor stream{io_context.get_executor(), ::fileno(file)};  
  boost::beast::http::async_write_header(  
      stream, serializer,  
      [](boost::system::error_code, std::size_t) {  
      });  
  io_context.run();  
  boost::beast::http::response_serializer<boost::beast::http::string_body> serializer2{std::move(serializer)};  
  std::fclose(file);  
}  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-04-26 15:06:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-826910974  

This is great, thank you

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-850857829  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: ednolan  
> Created at: 2021-06-03 18:17:08 UTC  
> Updated at: 2021-06-03 18:17:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-854079782  

I just re-tested the reproduction with latest commit on the develop branch (commit 30abfdb9) and the bug still occurs. Please de-stale

---

## Comment 6

> Username: stale[bot]  
> Created at: 2022-01-09 04:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-1008226704  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: ednolan  
> Created at: 2022-01-10 04:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-1008540289  

Tested with Beast commit a593738 and Boost version 1.78.0; issue still occurs.

---

## Comment 8

> Username: madmongo1  
> Created at: 2022-01-30 18:13:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-1025198588  

Reproduced.

---

## Comment 9

> Username: madmongo1  
> Created at: 2022-01-31 07:16:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-1025444666  

reproduced here for investigation:  
https://github.com/beast-issues/2221

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-01-31 17:53:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-1026048376  

why a separate organization called beast-issues?

---

## Comment 11

> Username: madmongo1  
> Created at: 2022-02-01 06:30:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-1026518351  

It helps my mental filing cabinet

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:20:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2221#issuecomment-1256864833  

@madmongo1 what's the state of this?
