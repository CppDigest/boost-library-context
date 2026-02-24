# #242 - compile fails if included boost/detail/basic_pointerbuf.hpp [Closed]

> Username: derofim  
> Created at: 2019-01-14 13:23:35 UTC  
> Updated at: 2019-01-15 17:51:59 UTC  
> Closed at: 2019-01-15 17:51:59 UTC  
> Url: https://github.com/boostorg/boost/issues/242  

Hello,  
  
Compile fails if included boost/detail/basic_pointerbuf.hpp  
  
**May be fixed with** #include <boost/integer.hpp> into basic_pointerbuf.hpp  
  
Boost version: 1.69.0  
  
Compiled with clang-6.0 and cmake:  
`findPackage( Boost 1.69.0 COMPONENTS program_options filesystem regex date_time system thread graph REQUIRED )`  
  
Installed boost at ubuntu 18.04 with:  
```  
cd ~  
wget https://dl.bintray.com/boostorg/release/1.69.0/source/boost_1_69_0.tar.bz2 \  
    && tar -xjf boost_1_69_0.tar.bz2 \  
    && rm -rf boost_1_69_0.tar.bz2 \  
    && cd boost_1_69_0 \  
    && sudo ./bootstrap.sh --prefix=/usr/ \  
    && sudo ./b2 link=shared install  
```  
  
Full log:  
<details><summary>CLICK ME</summary>  
<code>  
```  
/usr/include/boost/detail/basic_pointerbuf.hpp:79:28: error: no type named 'int_t' in namespace 'boost'  
   typedef typename boost::int_t<sizeof(way) * CHAR_BIT>::least cast_type;  
           ~~~~~~~~~~~~~~~~^~~~~  
/usr/include/boost/detail/basic_pointerbuf.hpp:79:33: error: expected unqualified-id                                                                  
   typedef typename boost::int_t<sizeof(way) * CHAR_BIT>::least cast_type;  
                                ^  
/usr/include/boost/detail/basic_pointerbuf.hpp:86:23: error: unknown type name 'cast_type'; did you mean 'base_type'?                                 
   switch(static_cast<cast_type>(way))  
                      ^  
/usr/include/boost/detail/basic_pointerbuf.hpp:33:20: note: 'base_type' declared here                                                                 
   typedef BufferT base_type;  
                   ^  
/usr/include/boost/detail/basic_pointerbuf.hpp:86:11: error: no matching conversion for static_cast from '::std::ios_base::seekdir'                   
      (aka 'std::_Ios_Seekdir') to 'boost::detail::basic_pointerbuf<char, std::__cxx11::basic_stringbuf<char> >::base_type'  
      (aka 'std::__cxx11::basic_stringbuf<char>')  
   switch(static_cast<cast_type>(way))  
          ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:139:15: note: in instantiation of member function                                
      'boost::detail::basic_pointerbuf<char, std::__cxx11::basic_stringbuf<char> >::seekoff' requested here  
        class lexical_istream_limited_src: boost::noncopyable {  
              ^  
/usr/include/boost/lexical_cast/try_lexical_convert.hpp:201:33: note: in instantiation of member function                                             
      'boost::detail::lexical_converter_impl<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >,  
      boost::uuids::uuid>::try_convert' requested here  
            return caster_type::try_convert(arg, result);  
                                ^  
/usr/include/boost/lexical_cast.hpp:41:41: note: in instantiation of function template specialization                                                 
      'boost::conversion::detail::try_lexical_convert<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >,  
      boost::uuids::uuid>' requested here  
        if (!boost::conversion::detail::try_lexical_convert(arg, result)) {  
                                        ^  
/home/denis/workspace/webrtc-test/src/net/WsListener.cpp:33:17: note: in instantiation of function template specialization                            
      'boost::lexical_cast<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::uuids::uuid>' requested here  
  return boost::lexical_cast<std::string>(genGuid());  
                ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/sstream:100:7: note: candidate constructor not viable: no known conversion from      
      '::std::ios_base::seekdir' (aka 'std::_Ios_Seekdir') to 'ios_base::openmode' (aka 'std::_Ios_Openmode') for 1st argument  
      basic_stringbuf(ios_base::openmode __mode = ios_base::in | ios_base::out)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/sstream:113:7: note: candidate constructor not viable: no known conversion from      
      '::std::ios_base::seekdir' (aka 'std::_Ios_Seekdir') to 'const std::__cxx11::basic_stringbuf<char, std::char_traits<char>,  
      std::allocator<char> >::__string_type' (aka 'const basic_string<char, std::char_traits<char>, std::allocator<char> >') for 1st argument  
      basic_stringbuf(const __string_type& __str,  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/sstream:120:7: note: candidate constructor not viable: no known conversion from      
      '::std::ios_base::seekdir' (aka 'std::_Ios_Seekdir') to 'const std::__cxx11::basic_stringbuf<char>' for 1st argument  
      basic_stringbuf(const basic_stringbuf&) = delete;  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/sstream:122:7: note: candidate constructor not viable: no known conversion from      
      '::std::ios_base::seekdir' (aka 'std::_Ios_Seekdir') to 'std::__cxx11::basic_stringbuf<char>' for 1st argument  
      basic_stringbuf(basic_stringbuf&& __rhs)  
      ^  
/usr/bin/../lib/gcc/x86_64-linux-gnu/8/../../../../include/c++/8/sstream:357:7: note: candidate constructor not viable: requires 2 arguments, but 1   
      was provided  
      basic_stringbuf(basic_stringbuf&& __rhs, __xfer_bufptrs&&)  
```  
</code>  
</details>
