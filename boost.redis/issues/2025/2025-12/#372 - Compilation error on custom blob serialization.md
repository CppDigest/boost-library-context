# #372 - Compilation error on custom blob serialization [Closed]

> Username: bruno-viva  
> Created at: 2025-12-13 23:48:26 UTC  
> Updated at: 2025-12-14 16:58:01 UTC  
> Closed at: 2025-12-14 16:58:00 UTC  
> Url: https://github.com/boostorg/redis/issues/372  

Hi! When upgrading from boost 1_87 to 1_90, the following code doesn't compile:  
  
```cpp  
#include <boost/redis.hpp>  
#include <boost/redis/connection.hpp>  
#include <boost/redis/request.hpp>  
#include <boost/redis/resp3/serialization.hpp>  
#include <boost/redis/src.hpp>  
  
struct Blob {  
  std::string data;  
};  
  
void boost_redis_to_bulk(std::string &to, const Blob &blob) {  
  boost::redis::resp3::boost_redis_to_bulk(to, blob.data);  
}  
  
void boost_redis_from_bulk(Blob &blob, std::string_view value,  
                           boost::system::error_code & /*error_code*/) {  
  blob.data = std::string(value);  
}  
  
int main() {  
  // Ignore correctness.. just trying to repro the compilation error here..  
  boost::redis::request request;  
  boost::redis::response<Blob> response;  
  boost::asio::io_context context;  
  boost::redis::connection conn(context);  
  conn.async_exec(request, response,  
                  [](boost::system::error_code const & /*ec*/, std::size_t) {  
                    //  
                  });  
  
  return 0;  
}  
```  
  
With compilation flags:  
  
`$ clang -std=c++17 blob.cpp -lc++ -stdlib=libc++ -fuse-ld=lld -lssl -lcrypto -L/usr/local/lib/ -static`  
  
With clang version:  
  
```  
$ clang --version  
Ubuntu clang version 18.1.3 (1ubuntu1)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
It yields to me:  
  
```  
In file included from blob.cpp:1:  
In file included from /usr/local/include/boost/redis.hpp:11:  
In file included from /usr/local/include/boost/redis/connection.hpp:10:  
In file included from /usr/local/include/boost/redis/adapter/adapt.hpp:10:  
In file included from /usr/local/include/boost/redis/adapter/detail/response_traits.hpp:10:  
In file included from /usr/local/include/boost/redis/adapter/detail/result_traits.hpp:10:  
/usr/local/include/boost/redis/adapter/detail/adapters.hpp:114:7: error: implicit instantiation of undefined template 'boost::redis::adapter::detail::converter<Blob>'  
  114 |       converter<T>::apply(t, node, ec);  
      |       ^  
/usr/local/include/boost/redis/adapter/detail/adapters.hpp:136:23: note: in instantiation of function template specialization 'boost::redis::adapter::detail::from_bulk_impl<Blob>::apply<std::string_view>' requested here  
  136 |    from_bulk_impl<T>::apply(t, node, ec);  
      |                       ^  
/usr/local/include/boost/redis/adapter/detail/adapters.hpp:229:7: note: in instantiation of function template specialization 'boost::redis::adapter::detail::boost_redis_from_bulk<Blob, std::string_view>' requested here  
  229 |       boost_redis_from_bulk(result, node, ec);  
      |       ^  
/usr/local/include/boost/redis/adapter/detail/adapters.hpp:513:13: note: in instantiation of function template specialization 'boost::redis::adapter::detail::simple_impl<Blob>::on_node<std::string_view>' requested here  
  513 |       impl_.on_node(result_->value(), nd, ec);  
      |             ^  
/usr/local/include/boost/redis/adapter/detail/response_traits.hpp:69:17: note: in instantiation of function template specialization 'boost::redis::adapter::detail::wrapper<boost::system::result<Blob, boost::redis::adapter::error>>::on_node<std::string_view>' requested here  
   69 |             arg.on_node(nd, ec);  
      |                 ^  
/usr/local/include/boost/redis/adapter/any_adapter.hpp:62:46: note: in instantiation of function template specialization 'boost::redis::adapter::detail::static_adapter<std::tuple<boost::system::result<Blob, boost::redis::adapter::error>>>::on_node<std::string_view>' requested here  
   62 |             case parse_event::node: adapter2.on_node(nd, ec); break;  
      |                                              ^  
/usr/local/include/boost/redis/adapter/any_adapter.hpp:85:12: note: in instantiation of function template specialization 'boost::redis::any_adapter::create_impl<std::tuple<boost::system::result<Blob, boost::redis::adapter::error>>>' requested here  
   85 |    : impl_(create_impl(resp))  
      |            ^  
/usr/local/include/boost/redis/connection.hpp:1049:30: note: in instantiation of function template specialization 'boost::redis::any_adapter::any_adapter<std::tuple<boost::system::result<Blob, boost::redis::adapter::error>>, void>' requested here  
 1049 |       return async_exec(req, any_adapter{resp}, std::forward<CompletionToken>(token));  
      |                              ^  
/usr/local/include/boost/redis/adapter/detail/adapters.hpp:53:8: note: template is declared here  
   53 | struct converter;  
      |        ^  
1 error generated.  
```  
  
What could I be missing? Thanks in advance!

---

## Comment 1

> Username: bruno-viva  
> Created at: 2025-12-13 23:56:17 UTC  
> Url: https://github.com/boostorg/redis/issues/372#issuecomment-3649945616  

1_87 succeeds: https://godbolt.org/z/qjPY6naqM  
1_88 fails: https://godbolt.org/z/qzbffjrqj

---

## Comment 2

> Username: bruno-viva  
> Created at: 2025-12-14 02:03:08 UTC  
> Url: https://github.com/boostorg/redis/issues/372#issuecomment-3650056416  

Adding this to the code made it compile. But I am not sure this was intended?  
  
```cpp  
// Boost.Redis >= 1.90: adapter uses converter<T>::apply(T&, node, ec)  
namespace boost::redis::adapter::detail {  
template <>  
struct converter<::Blob> {  
  using node_type = boost::redis::resp3::basic_node<std::string_view>;  
  
  static void apply(::Blob& out, node_type const& node, boost::system::error_code& ec) {  
    ::boost_redis_from_bulk(out, node.value, ec);  
  }  
  
  static void apply(std::string& out, ::Blob const& in, boost::system::error_code& ec) {  
    (void)ec;  
    ::boost_redis_to_bulk(out, in);  
  }  
};  
}  // namespace boost::redis::adapter::detail  
```  
  
https://godbolt.org/z/fMKenMW9T

---

## Comment 3

> Username: anarthal  
> Created at: 2025-12-14 02:31:36 UTC  
> Url: https://github.com/boostorg/redis/issues/372#issuecomment-3650081651  

Using stuff from the detail namespace is never intended. This looks like a legitime bug. @mzimbres probably knows more about this one.

---

## Comment 4

> Username: mzimbres  
> Created at: 2025-12-14 14:33:14 UTC  
> Url: https://github.com/boostorg/redis/issues/372#issuecomment-3651225369  

Hi Bruno, I don't know why it worked before but the second parameter below should be a `node_view`  
  
```cpp  
// Wrong  
void boost_redis_from_bulk(Blob &blob, std::string_view value, error_code & /*error_code*/)  
```  
versus  
```cpp  
// Correct  
void boost_redis_from_bulk(Blob& blob, node_view const& value, error_code&)  
```  
Does this solve your problem?

---

## Comment 5

> Username: bruno-viva  
> Created at: 2025-12-14 16:58:00 UTC  
> Url: https://github.com/boostorg/redis/issues/372#issuecomment-3651646013  

> Hi Bruno, I don't know why it worked before but the second parameter below should be a `node_view`  
>   
> // Wrong  
> void boost_redis_from_bulk(Blob &blob, std::string_view value, error_code & /*error_code*/)  
> versus  
>   
> // Correct  
> void boost_redis_from_bulk(Blob& blob, node_view const& value, error_code&)  
> Does this solve your problem?  
  
It solves it! Thanks so much! Not sure why it worked before and why I was using a string_view instead.
