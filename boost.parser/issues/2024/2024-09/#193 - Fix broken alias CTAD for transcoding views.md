# #193 - Fix broken alias CTAD for transcoding views [Closed]

> Username: tzlaine  
> Created at: 2024-09-30 21:31:06 UTC  
> Updated at: 2024-09-30 23:06:47 UTC  
> Closed at: 2024-09-30 23:06:47 UTC  
> Url: https://github.com/boostorg/parser/issues/193  

This:  
  
```c++  
    template<class V>  
    using utf8_view = detail::text::utf_view<format::utf8, V>;  
    template<class V>  
    using utf16_view = detail::text::utf_view<format::utf16, V>;  
    template<class V>  
    using utf32_view = detail::text::utf_view<format::utf32, V>;  
```  
  
despite coming from a knowledgeable committee member, does not work.  We should go back to the previous scheme of just having three distinct class types.
