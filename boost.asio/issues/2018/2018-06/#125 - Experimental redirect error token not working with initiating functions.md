# #125 - Experimental redirect error token not working with initiating functions [Closed]

> Username: cmazakas  
> Created at: 2018-06-23 20:30:29 UTC  
> Updated at: 2018-06-27 15:27:45 UTC  
> Closed at: 2018-06-27 15:20:14 UTC  
> Url: https://github.com/boostorg/asio/issues/125  

If a user of Asio attempts to use a custom initiating function, it fails to compile when using the redirect error token type.  
  
We can easily recreate this using Win10's Windows Subystem for Linux with clang-6.  
  
Code:  
```  
#include <boost/asio.hpp>  
#include <boost/asio/experimental.hpp>  
#include <boost/system/error_code.hpp>  
#include <utility>  
  
namespace asio = boost::asio;  
  
template <typename Handler>  
auto initiating_function(  
  asio::io_context& io,  
  Handler&& handler  
) {  
  asio::async_completion<  
    Handler, void(boost::system::error_code)  
  >  
  init(handler);  
  
  co_spawn(  
    io,  
    [handler = std::move(init.completion_handler)]  
    () mutable -> asio::experimental::awaitable<void> {  
       co_return handler(boost::system::error_code());  
    },  
    asio::experimental::detached);  
  
  return init.result.get();  
}  
  
auto coro_test(  
  asio::io_context& io  
) -> asio::experimental::awaitable<void> {  
  
  auto token = co_await asio::experimental::this_coro::token();  
  
  auto ec = boost::system::error_code();  
  
  auto error_token = asio::experimental::redirect_error(token, ec);  
  
  co_await initiating_function(io, error_token);  
  
  co_return;  
}  
  
int main() {  
  asio::io_context io;  
  
  asio::experimental::co_spawn(  
    io,  
    [&]() mutable { return coro_test(io); },  
    asio::experimental::detached);  
  
  io.run();  
  
  return 0;  
}  
```  
  
Compile command:  
```  
chris@DESKTOP-752NP2V:~/cpp$ ~/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/bin/clang++ -I/home/chris/boosts/i  
nstall-ftw/include -I/home/chris/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/include/c++/v1/ -L/home/chris/cl  
ang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/lib/ -lc++ -lc++abi -pthread -std=c++17 -nostdinc++ -fcoroutines-ts  
 asio-fail.cpp /home/chris/boosts/install-ftw/lib/libboost_system.a  
```  
  
Output:  
```  
asio-fail.cpp:39:3: warning: expression result unused [-Wunused-value]  
  co_await initiating_function(io, error_token);  
  ^~~~~~~~  
In file included from asio-fail.cpp:2:  
In file included from /home/chris/boosts/install-ftw/include/boost/asio/experimental.hpp:18:  
In file included from /home/chris/boosts/install-ftw/include/boost/asio/experimental/co_spawn.hpp:224:  
/home/chris/boosts/install-ftw/include/boost/asio/experimental/impl/co_spawn.hpp:341:20: error: no matching  
      constructor for initialization of 'std::__1::tuple<>'  
    new (&result_) T(std::forward<U>(u));  
                   ^ ~~~~~~~~~~~~~~~~~~  
/home/chris/boosts/install-ftw/include/boost/asio/experimental/impl/co_spawn.hpp:606:21: note: in instantiation of  
      function template specialization 'boost::asio::experimental::detail::awaitee<std::__1::tuple<>,  
      boost::asio::strand<boost::asio::executor> >::return_value<std::__1::tuple<boost::system::error_code &&> >'  
      requested here  
    this->awaitee_->return_value(  
                    ^  
/home/chris/boosts/install-ftw/include/boost/asio/experimental/impl/redirect_error.hpp:53:5: note: in instantiation of  
      function template specialization  
      'boost::asio::experimental::detail::await_handler<boost::asio::strand<boost::asio::executor>>::operator()<boost:  
:system::error_code>'  
      requested here  
    handler_(BOOST_ASIO_MOVE_CAST(Args)(args)...);  
    ^  
asio-fail.cpp:22:18: note: in instantiation of function template specialization  
      'boost::asio::experimental::detail::redirect_error_handler<boost::asio::experimental::detail::await_handler<boos  
t::asio::strand<boost::asio::executor>>  
      >::operator()<boost::system::error_code>' requested here  
       co_return handler(boost::system::error_code());  
                 ^  
/home/chris/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/include/c++/v1/tuple:911:28: note: candidate  
      constructor (the implicit copy constructor) not viable: no known conversion from  
      'tuple<boost::system::error_code &&>' to 'const tuple<(no argument)>' for 1st argument  
class _LIBCPP_TEMPLATE_VIS tuple<>  
                           ^  
/home/chris/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/include/c++/v1/tuple:911:28: note: candidate  
      constructor (the implicit move constructor) not viable: no known conversion from  
      'tuple<boost::system::error_code &&>' to 'tuple<(no argument)>' for 1st argument  
/home/chris/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/include/c++/v1/tuple:924:9: note: candidate template  
      ignored: could not match 'array' against 'tuple'  
        tuple(array<_Up, 0>) _NOEXCEPT {}  
        ^  
/home/chris/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/include/c++/v1/tuple:918:9: note: candidate  
      constructor template not viable: requires 2 arguments, but 1 was provided  
        tuple(allocator_arg_t, const _Alloc&) _NOEXCEPT {}  
        ^  
/home/chris/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/include/c++/v1/tuple:921:9: note: candidate  
      constructor template not viable: requires 3 arguments, but 1 was provided  
        tuple(allocator_arg_t, const _Alloc&, const tuple&) _NOEXCEPT {}  
        ^  
/home/chris/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/include/c++/v1/tuple:927:9: note: candidate  
      constructor template not viable: requires 3 arguments, but 1 was provided  
        tuple(allocator_arg_t, const _Alloc&, array<_Up, 0>) _NOEXCEPT {}  
        ^  
/home/chris/clang/clang+llvm-6.0.0-x86_64-linux-gnu-ubuntu-16.04/include/c++/v1/tuple:915:23: note: candidate  
      constructor not viable: requires 0 arguments, but 1 was provided  
    _LIBCPP_CONSTEXPR tuple() _NOEXCEPT {}  
                      ^  
1 warning and 1 error generated.  
```  
The code compiles perfectly when using `token` in lieu of `error_token`.  
  
Is there a way to get this fixed? It makes using Asio's coros with Beast impossible or any other custom code.

---

## Comment 1

> Username: cmazakas  
> Created at: 2018-06-24 03:12:51 UTC  
> Url: https://github.com/boostorg/asio/issues/125#issuecomment-399725450  

So @chriskohlhoff, I noticed something. You cast your error codes to `error_code const&`. This seems like it's _required_ for clang-6 on Linux. So to keep things compliant, it seems like one must follow your example and use `handler_(static_cast<error_code const&>(ec))` in order to make the redirect error token work with initiating functions.  
  
Not very fun to debug but I'm incredibly pleased I was able to figure and I hope my struggles will be googled and found by some other poor soul.  
  
Also, the Asio need to reflect this at least. I mean, yeesh.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-06-24 03:32:38 UTC  
> Url: https://github.com/boostorg/asio/issues/125#issuecomment-399726332  

@LeonineKing1199 Where are these error codes being cast, can you please provide a link?

---

## Comment 3

> Username: cmazakas  
> Created at: 2018-06-27 15:20:14 UTC  
> Url: https://github.com/boostorg/asio/issues/125#issuecomment-400713339  

Hmm, I'm thinking about closing this issue as it might just be a defect in the WSL.  
  
For example, both wandbox.org and Compiler Explorer can compile the code in question with no issues.  
  
I also attempted with a local Linux server and compilation was successful as well. This leads me to believe that there's more or less an issue just on the WSL side.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-06-27 15:27:45 UTC  
> Url: https://github.com/boostorg/asio/issues/125#issuecomment-400716347  

Please go through the appropriate Microsoft channels to inform them of this defect.
