# #96 - Frame module name not accessible [Closed]

> Username: nminkov  
> Created at: 2020-07-08 13:19:35 UTC  
> Updated at: 2020-08-21 21:10:17 UTC  
> Closed at: 2020-08-21 21:10:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/96  

On Windows, frame have an associated module name. Please give public access to it. E.g. frame.module_name()

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-08-20 15:06:26 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/96#issuecomment-677722357  

Try using the [boost::dll::symbol_location_ptr](https://www.boost.org/doc/libs/1_74_0/doc/html/boost/dll/symbol_location_ptr.html) on a result of the frame::address() call.  
  
Does this solves your issue?

---

## Comment 2

> Username: nminkov  
> Created at: 2020-08-21 09:31:21 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/96#issuecomment-678142001  

Thanks for the suggestion, which indeed provides the module full path.    
  
My issue now is that I don't remember why asked the question, because in my code I already use  boost::stacktrace::detail::debugging_symbols, get_name_impl(frameAddress, &moduleName) method, which does that too.  July 8 for me it's like... 10 code years ago.

---

## Comment 3

> Username: apolukhin  
> Created at: 2020-08-21 21:10:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/96#issuecomment-678502994  

:)  
  
It's better not to use stuff from `detail` namespace as it is a subject to silent change.  
  
Anyway, thanks for the report!
