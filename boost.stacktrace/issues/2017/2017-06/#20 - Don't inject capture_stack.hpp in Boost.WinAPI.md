# #20 - Don't inject capture_stack.hpp in Boost.WinAPI [Closed]

> Username: Lastique  
> Created at: 2017-06-22 21:37:39 UTC  
> Updated at: 2017-07-01 18:55:02 UTC  
> Closed at: 2017-07-01 18:55:02 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/20  

Please, move boost/detail/winapi/capture_stack.hpp to boost/stacktrace and also update the file contents to:  
  
- avoid injecting symbols into `boost::detail::winapi` namespace  
- avoid using `BOOST_DETAIL_WINAPI` prefix in macros  
- fix the include guard

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-06-24 08:26:22 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/20#issuecomment-310824884  

I'd rather move it to Boost.WinAPI.  
  
What do you think about it?

---

## Comment 2

> Username: Lastique  
> Created at: 2017-06-24 14:40:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/20#issuecomment-310842465  

I've added the function to Boost.WinAPI.
