# #170 - Warnings in include/boost/core/detail/string_view.hpp [Closed]

> Username: SoulfreezerXP  
> Created at: 2024-04-10 05:37:59 UTC  
> Updated at: 2024-04-11 10:44:23 UTC  
> Closed at: 2024-04-11 10:44:23 UTC  
> Url: https://github.com/boostorg/core/issues/170  

Clangd finds warnings regarding sign conversions in string_view.hpp (boost 1.84), which I can't turn off. Neither "clang diagnostic ignored" nor "-isystem" have any influence. Maybe it's a specific problem with clangd, but you should still eliminate the warnings in the boost code.  
  
Check this link for more details:  
https://stackoverflow.com/questions/78298063/boost-beast-1-84-server-example-and-clangd-warning-in-qt-creator-13  
  
It is about this code excerpt in include/boost/core/detail/string_view.hpp:  
  
    BOOST_CXX14_CONSTEXPR size_type find( Ch c, size_type pos = 0 ) const BOOST_NOEXCEPT  
    {  
        if( pos >= size() ) return npos;  
  
        Ch const* r = traits_type::find( data() + pos, size() - pos, c );  
  
        return r? r - data(): npos; //HERE  
    }  
  
    BOOST_CXX14_CONSTEXPR size_type find( Ch const* s, size_type pos, size_type n ) const BOOST_NOEXCEPT  
    {  
        if( n == 1 ) return find( s[0], pos );  
  
        if( pos + n > size() ) return npos;  
        if( n == 0 ) return pos;  
  
        Ch const* p = data() + pos;  
        Ch const* last = data() + size() - n + 1;  
  
        for( ;; )  
        {  
            p = traits_type::find( p, last - p, s[0] ); //HERE  
  
            if( p == 0 ) break;  
  
            if( traits_type::compare( p + 1, s + 1, n - 1 ) == 0 ) return p - data(); //HERE  
  
            ++p;  
        }  
  
        return npos;  
    }
