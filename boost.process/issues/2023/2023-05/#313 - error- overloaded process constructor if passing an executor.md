# #313 - [v2] error: overloaded process constructor if passing an executor [Closed]

> Username: rbrugo  
> Created at: 2023-05-08 10:52:01 UTC  
> Updated at: 2023-06-28 12:24:16 UTC  
> Closed at: 2023-06-28 12:24:16 UTC  
> Url: https://github.com/boostorg/process/issues/313  

In the following code the first `process` constructor succeeds, the second fails:  
```cpp  
#include <boost/asio/awaitable.hpp>  
#include <boost/process/v2.hpp>  
  
void ok()  
{  
    auto ctx = boost::asio::io_context{};  
    boost::process::v2::process(ctx, "", {});  
}  
  
auto fail() -> boost::asio::awaitable<void>  
{  
    auto ex = co_await boost::asio::this_coro::executor;  
    boost::process::v2::process(ex, "", {});  
}  
```  
With the error:  
```  
call of overloaded 'basic_process(boost::asio::any_io_executor&, const char [1], <brace-enclosed initializer list>)' is ambiguous  
note: candidate: 'boost::process::v2::basic_process<Executor>::basic_process(executor_type, const boost::filesystem::path&, std::initializer_list<boost::basic_string_view<wchar_t, std::char_traits<wchar_t> > >, Inits&& ...) [with Inits = {}; Executor = boost::asio::any_io_executor; executor_type = boost::asio::any_io_executor]'  
note: candidate: 'boost::process::v2::basic_process<Executor>::basic_process(executor_type, const boost::filesystem::path&, std::initializer_list<boost::basic_string_view<char, std::char_traits<char> > >, Inits&& ...) [with Inits = {}; Executor = boost::asio::any_io_executor; executor_type = boost::asio::any_io_executor]'  
```  
  
Compiler explorer: https://gcc.godbolt.org/z/sddfdo64s

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-05-08 10:52:52 UTC  
> Url: https://github.com/boostorg/process/issues/313#issuecomment-1538170005  

Thanks for reporting, I'll check.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-06-13 13:26:29 UTC  
> Updated at: 2023-06-13 13:27:06 UTC  
> Url: https://github.com/boostorg/process/issues/313#issuecomment-1589316274  

This isn't trivial to solve. How do you feel about just allowing `process(ctx, "")` if you want zero args?  
  
Alternatively `process(ctx, "", no_args)`.
