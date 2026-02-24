# #284 Fix error in drone CI [Merged]

> Username: andreasbuhr  
> Created at: 2025-10-15 07:11:07 UTC  
> Updated at: 2025-10-31 20:39:47 UTC  
> Merged at: 2025-10-31 20:39:46 UTC  
> Closed at: 2025-10-31 20:39:46 UTC  
> Url: https://github.com/boostorg/parser/pull/284  

error: call to implicitly-deleted copy constructor of [snip]  
constexpr closure(F f) : f_(f) {}  
  
Full error was:  
  
```  
In file included from libs/parser/test/transform_replace.cpp:9:  
  
In file included from ./boost/parser/transform_replace.hpp:4:  
  
In file included from ./boost/parser/replace.hpp:4:  
  
In file included from ./boost/parser/search.hpp:4:  
  
In file included from ./boost/parser/parser.hpp:9:  
  
In file included from ./boost/parser/parser_fwd.hpp:10:  
  
In file included from ./boost/parser/error_handling_fwd.hpp:6:  
  
In file included from ./boost/parser/detail/text/transcode_view.hpp:14:  
  
./boost/parser/detail/stl_interfaces/view_adaptor.hpp:262:34: error: call to implicitly-deleted copy constructor of 'boost::parser::detail::stl_interfaces::detail::bind_back_t<boost::parser::detail::transform_replace_impl, boost::parser::detail::text::utf16_view<boost::parser::detail::text::detail::owning_view<boost::parser::subrange<const char *, boost::parser::detail::text::null_sentinel_t>>>, boost::parser::parser_interface<boost::parser::delimited_seq_parser<boost::parser::int_parser<int, 10, 1, -1>, boost::parser::omit_parser<boost::parser::char_parser<char>>>>, boost::parser::parser_interface<boost::parser::ws_parser<false, false>>, (lambda at libs/parser/test/transform_replace.cpp:116:17)>'  
  
        constexpr closure(F f) : f_(f) {}  
```

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-10-31 20:39:38 UTC  
> Url: https://github.com/boostorg/parser/pull/284#issuecomment-3474820957  

Nice catch.

---
