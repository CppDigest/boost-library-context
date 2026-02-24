# #296 - Compilation errors with Boost 1.64 [Closed]

> Username: cdmh  
> Created at: 2017-04-04 11:12:42 UTC  
> Updated at: 2017-04-26 22:30:20 UTC  
> Closed at: 2017-04-26 22:30:20 UTC  
> Url: https://github.com/boostorg/beast/issues/296  

Currently in Beta, Boost 1.64 breaks Beast  
  
Using VC14.1 (Visual Studio 2017)  
  
`Beast\include\beast/websocket/detail/decorator.hpp(105): error C2280: 'boost::basic_string_ref<char,std::char_traits<char>>::basic_string_ref<std::allocator<char>>(std::basic_string<char,std::char_traits<char>,std::allocator<char>> &&)': attempting to reference a deleted function`  
  
`Beast\include\beast/websocket/detail/decorator.hpp(118): error C2280: 'boost::basic_string_ref<char,std::char_traits<char>>::basic_string_ref<std::allocator<char>>(std::basic_string<char,std::char_traits<char>,std::allocator<char>> &&)': attempting to reference a deleted function`  
  
`Beast\include\beast/websocket/impl/stream.ipp(189): error C2280: 'boost::basic_string_ref<char,std::char_traits<char>>::basic_string_ref<std::allocator<char>>(std::basic_string<char,std::char_traits<char>,std::allocator<char>> &&)': attempting to reference a deleted function`  
  
`Beast\include\beast/http/basic_fields.hpp(266): error C2280: 'boost::basic_string_ref<char,std::char_traits<char>>::basic_string_ref<std::allocator<char>>(std::basic_string<char,std::char_traits<char>,std::allocator<char>> &&)': attempting to reference a deleted function`

---

## Comment 1

> Username: HowardHinnant  
> Created at: 2017-04-04 19:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/296#issuecomment-291601013  

This _should_ be fixed by this: http://lists.boost.org/Archives/boost/2017/03/234022.php but isn't yet.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-04-24 17:21:52 UTC  
> Url: https://github.com/boostorg/beast/issues/296#issuecomment-296755716  

Is this still an issue in the release version of Boost 1.64.0?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-26 22:30:20 UTC  
> Url: https://github.com/boostorg/beast/issues/296#issuecomment-297560006  

It looks like this is fixed so I'll close the issue. Please re-open it if a problem remains, thanks!
