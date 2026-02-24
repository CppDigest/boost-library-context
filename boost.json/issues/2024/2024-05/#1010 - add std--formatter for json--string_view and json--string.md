# #1010 - add  std::formatter for json::string_view and json::string [Open]

> Username: hj2000  
> Created at: 2024-05-27 06:30:54 UTC  
> Updated at: 2024-05-27 08:28:18 UTC  
> Url: https://github.com/boostorg/json/issues/1010  

We can print json::string and json::string_view with ostream,but we cann't use std::format to format them ,could we have std::formatter<json::string> and std::formatter<json::string_view>?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-05-27 08:28:17 UTC  
> Url: https://github.com/boostorg/json/issues/1010#issuecomment-2132937741  

For future reference:  
  
`std::format(fmt, args...)` defers to `std::vformat(fmt.get(), make_format_args(args...))`.  
  
```c++  
template< class Context = std::format_context), class... Args >  
format-arg-store make_format_args( Args&... args );  
```  
`typename Context::template formatter_type<std::remove_cv<Argi>>` should be a [_BasicFormatter_](https://en.cppreference.com/w/cpp/named_req/BasicFormatter). `format_context::formatter_type<T>` is [`std::formatter<T, char>`](http://en.cppreference.com/w/cpp/utility/format/formatter). Curiously, _BasicFormatters_ are only required to work with instantiations of [`std::basic_format_context`](http://en.cppreference.com/w/cpp/utility/format/basic_format_context) as `Context`.  
  
So, it seems like the formatter should be something like this:  
```c++  
namespace std  
{  
template<>  
struct formatter<boost::json::string, char>  
{  
    template<class OIt>  
    OIt  
    format(boost::json::string const&, std::basic_format_context<OIt, char>&);  
};  
}  
```  
  
If we want to also support `{fmt}`, we need basically the same thing, only in a different namespace, so, better do:  
```c++  
namespace boost::json::detail  
{  
struct formatter<string>  
{  
    template<class Context>  
    Context::iterator  
    format(string const&, Context&);  
};  
}  
namespace std { template<> struct formatter<boost::json::string, char> : boost::json::detail::formatter<boost::json::string> {}; }  
namespace fmt { template<> struct formatter<boost::json::string> : boost::json::detail::formatter<boost::json::string> {}; }  
```
