# #46 - lexical_cast throws std::bad_cast [Closed]

> Username: apolukhin  
> Created at: 2021-05-02 17:29:08 UTC  
> Updated at: 2024-02-15 08:50:11 UTC  
> Closed at: 2024-02-15 08:50:11 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/46  

lexical_cast always throws std::bad_cast exception while converting values of type std::basic_string<char, ci_char_traits> (where ci_char_traits is derived from std::char_traits<char>) to double on Linux. It works on windows though(i.e. throws boost::bad_lexcial_cast exception for bad values, and returns double for valid values). It also works on Linux with boost 1.57. I noticed there has been a change in lexical_cast implementation that causes this issue now. std::bad_cast exception is thrown here because locale associated with the stream does not has std::num_get<char, std::istreambuf_iterator<char, ci_char_traits> > > facet as shown in the stack trace below. It works with std::string (std::basic_string<char, std::char_traits<char>) > ) because locale has std::num_get<char, std::istreambuf_iterator<char, std::char_traits<char>> > facet.  
```  
#0 cxxabiv1::cxa_throw (obj=0x60b0c0, tinfo=0x60a3f0 <_ZTISt8bad_cast@@GLIBCXX_3.4>,  
    dest=0x2aaaaab092e0 <std::bad_cast::~bad_cast()>) at ../../../../gcc-4.8.1/libstdc++-v3/libsupc++/eh_throw.cc:71  
  
#1 0x00002aaaaab5fb62 in std::throw_bad_cast ()  
    at ../../../../../gcc-4.8.1/libstdc++-v3/src/c++11/functexcept.cc:56  
  
#2 0x00000000004051bc in std::check_facet<std::num_get<char, std::istreambuf_iterator<char, ci_char_traits> > > (f=0x0) at /xxxx/xxxx/xxxx/gcc-4.8.1/include/c++/4.8.1/bits/basic_ios.h:49 #3 0x0000000000404b3f in std::basic_istream<char, ci_char_traits>::_M_extract<double> (this=0x7fffffffe000,  
    v=@0x7fffffffe228: 0) at /xxxx/xxxx/xxxx/gcc-4.8.1/include/c++/4.8.1/bits/istream.tcc:95  
  
#4 0x0000000000404397 in std::basic_istream<char, ci_char_traits>::operator>> (this=0x7fffffffe000,  
    f=@0x7fffffffe228: 0) at /xxxx/xxxx/xxxx/gcc-4.8.1/include/c++/4.8.1/istream:219  
  
#5 0x0000000000403b84 in boost::detail::lexical_ostream_limited_src<char, ci_char_traits>::shr_using_base_class<double> (this=0x7fffffffe1a0, output=@0x7fffffffe228: 0)  
    at /xxxx/xxxx/xxxx/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:600  
  
#6 0x0000000000403503 in boost::detail::lexical_ostream_limited_src<char, ci_char_traits>::float_types_converter_internal<double> (this=0x7fffffffe1a0, output=@0x7fffffffe228: 0)  
    at /xxxx/xxxx/xxxx/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:744  
  
#7 0x0000000000403145 in boost::detail::lexical_ostream_limited_src<char, ci_char_traits>::operator>> (  
    this=0x7fffffffe1a0, output=@0x7fffffffe228: 0) at /xxxx/xxxx/xxxx/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:770  
  
#8 0x0000000000402cce in boost::detail::lexical_converter_impl<double, std::basic_string<char, ci_char_traits, std::allocator<char> > >::try_convert (arg=..., result=@0x7fffffffe228: 0)  
    at /xxxx/xxxx/xxxx/include/boost/lexical_cast/detail/converter_lexical.hpp:485  
  
#9 0x0000000000402a32 in boost::conversion::detail::try_lexical_convert<double, std::basic_string<char, ci_char_traits, std::allocator<char> > > (arg=..., result=@0x7fffffffe228: 0)  
    at /xxxx/xxxx/xxxx/include/boost/lexical_cast/try_lexical_convert.hpp:196  
  
#10 0x00000000004027be in boost::lexical_cast<double, std::basic_string<char, ci_char_traits, std::allocator<char> > > (arg=...) at /xxxx/xxxx/xxxx/include/boost/lexical_cast.hpp:41 #11 0x0000000000401d7f in lexicalcast (mystr=...) at lexicalcast_test.cpp:14 #12 0x0000000000401f0d in main () at lexicalcast_test.cpp:39  
```  
  
Issue from trac https://svn.boost.org/trac10/ticket/13366
