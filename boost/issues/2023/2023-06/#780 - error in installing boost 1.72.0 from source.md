# #780 - error in installing boost 1.72.0 from source [Closed]

> Username: monajalal  
> Created at: 2023-06-05 19:40:42 UTC  
> Updated at: 2023-06-06 15:18:09 UTC  
> Closed at: 2023-06-06 15:18:09 UTC  
> Url: https://github.com/boostorg/boost/issues/780  

could you please help me install boost 1.72.0 from source in ubuntu 22.04?  
  
```  
(base) mona@ard-gpu-01:~/boost_1_72_0$ sudo ./b2  
  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
./boost/wave/util/flex_string.hpp: In function ‘void boost::wave::util::flex_string<E, T, A, Storage>::InsertImpl(boost::wave::util::flex_string<E, T, A, Storage>::iterator, FwdIterator, FwdIterator, std::forward_iterator_tag) [with FwdIterator = const char*; E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’:  
./boost/wave/util/flex_string.hpp:535:1: note: declared here  
  535 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/x86_64-linux-gnu/c++/11/bits/c++allocator.h:33,  
                 from /usr/include/c++/11/bits/allocator.h:46,  
                 from /usr/include/c++/11/string:41,  
                 from /usr/include/c++/11/bits/locale_classes.h:40,  
                 from /usr/include/c++/11/bits/ios_base.h:41,  
                 from /usr/include/c++/11/streambuf:41,  
                 from /usr/include/c++/11/bits/streambuf_iterator.h:35,  
                 from /usr/include/c++/11/iterator:66,  
                 from ./boost/iterator/iterator_categories.hpp:23,  
                 from ./boost/iterator/iterator_adaptor.hpp:14,  
                 from ./boost/iterator/reverse_iterator.hpp:10,  
                 from ./boost/wave/util/flex_string.hpp:90,  
                 from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
In member function ‘void __gnu_cxx::new_allocator<_Tp>::deallocate(_Tp*, __gnu_cxx::new_allocator<_Tp>::size_type) [with _Tp = char]’,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::Free(void*, boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:566:22,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<E, A>::~AllocatorStringStorage() [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:648:17,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::reserve(boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:702:5,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::append(ForwardIterator, ForwardIterator) [with ForwardIterator = const char*; E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:715:20,  
    inlined from ‘void boost::wave::util::CowString<Storage, Align>::append(FwdIterator, FwdIterator) [with FwdIterator = const char*; Storage = boost::wave::util::AllocatorStringStorage<char>; Align = char*]’ at ./boost/wave/util/flex_string.hpp:1374:22,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::InsertImpl(boost::wave::util::flex_string<E, T, A, Storage>::iterator, FwdIterator, FwdIterator, std::forward_iterator_tag) [with FwdIterator = const char*; E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1920:28:  
/usr/include/c++/11/ext/new_allocator.h:145:26: warning: ‘void operator delete(void*, std::size_t)’ called on unallocated object ‘boost::wave::util::SimpleStringStorage<char, std::allocator<char> >::emptyString_’ [-Wfree-nonheap-object]  
  145 |         ::operator delete(__p  
      |         ~~~~~~~~~~~~~~~~~^~~~  
  146 | #if __cpp_sized_deallocation  
      | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                           , __t * sizeof(_Tp)  
      |                           ~~~~~~~~~~~~~~~~~~~  
  148 | #endif  
      | ~~~~~~                      
  149 |                          );  
      |                          ~  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
./boost/wave/util/flex_string.hpp: In function ‘void boost::wave::util::flex_string<E, T, A, Storage>::InsertImpl(boost::wave::util::flex_string<E, T, A, Storage>::iterator, FwdIterator, FwdIterator, std::forward_iterator_tag) [with FwdIterator = const char*; E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’:  
./boost/wave/util/flex_string.hpp:535:1: note: declared here  
  535 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/x86_64-linux-gnu/c++/11/bits/c++allocator.h:33,  
                 from /usr/include/c++/11/bits/allocator.h:46,  
                 from /usr/include/c++/11/string:41,  
                 from /usr/include/c++/11/bits/locale_classes.h:40,  
                 from /usr/include/c++/11/bits/ios_base.h:41,  
                 from /usr/include/c++/11/streambuf:41,  
                 from /usr/include/c++/11/bits/streambuf_iterator.h:35,  
                 from /usr/include/c++/11/iterator:66,  
                 from ./boost/iterator/iterator_categories.hpp:23,  
                 from ./boost/iterator/iterator_adaptor.hpp:14,  
                 from ./boost/iterator/reverse_iterator.hpp:10,  
                 from ./boost/wave/util/flex_string.hpp:90,  
                 from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
In member function ‘void __gnu_cxx::new_allocator<_Tp>::deallocate(_Tp*, __gnu_cxx::new_allocator<_Tp>::size_type) [with _Tp = char]’,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::Free(void*, boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:566:22,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<E, A>::~AllocatorStringStorage() [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:648:17,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::reserve(boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:702:5,  
    inlined from ‘void boost::wave::util::CowString<Storage, Align>::reserve(boost::wave::util::CowString<Storage, Align>::size_type) [with Storage = boost::wave::util::AllocatorStringStorage<char>; Align = char*]’ at ./boost/wave/util/flex_string.hpp:1381:23,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::reserve(boost::wave::util::flex_string<E, T, A, Storage>::size_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1602:25,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::push_back(boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1691:20,  
    inlined from ‘boost::wave::util::flex_string<E, T, A, Storage>& boost::wave::util::flex_string<E, T, A, Storage>::operator+=(boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1639:18,  
    inlined from ‘boost::wave::token_id boost::wave::cpplexer::re2clex::scan(boost::wave::cpplexer::re2clex::Scanner<Iterator>*) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >]’ at cpp.re:524:28:  
/usr/include/c++/11/ext/new_allocator.h:145:26: warning: ‘void operator delete(void*, std::size_t)’ called on unallocated object ‘boost::wave::util::SimpleStringStorage<char, std::allocator<char> >::emptyString_’ [-Wfree-nonheap-object]  
  145 |         ::operator delete(__p  
      |         ~~~~~~~~~~~~~~~~~^~~~  
  146 | #if __cpp_sized_deallocation  
      | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                           , __t * sizeof(_Tp)  
      |                           ~~~~~~~~~~~~~~~~~~~  
  148 | #endif  
      | ~~~~~~                      
  149 |                          );  
      |                          ~  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
./boost/wave/util/flex_string.hpp: In function ‘boost::wave::token_id boost::wave::cpplexer::re2clex::scan(boost::wave::cpplexer::re2clex::Scanner<Iterator>*) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >]’:  
./boost/wave/util/flex_string.hpp:535:1: note: declared here  
  535 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/x86_64-linux-gnu/c++/11/bits/c++allocator.h:33,  
                 from /usr/include/c++/11/bits/allocator.h:46,  
                 from /usr/include/c++/11/string:41,  
                 from /usr/include/c++/11/bits/locale_classes.h:40,  
                 from /usr/include/c++/11/bits/ios_base.h:41,  
                 from /usr/include/c++/11/streambuf:41,  
                 from /usr/include/c++/11/bits/streambuf_iterator.h:35,  
                 from /usr/include/c++/11/iterator:66,  
                 from ./boost/iterator/iterator_categories.hpp:23,  
                 from ./boost/iterator/iterator_adaptor.hpp:14,  
                 from ./boost/iterator/reverse_iterator.hpp:10,  
                 from ./boost/wave/util/flex_string.hpp:90,  
                 from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
In member function ‘void __gnu_cxx::new_allocator<_Tp>::deallocate(_Tp*, __gnu_cxx::new_allocator<_Tp>::size_type) [with _Tp = char]’,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::Free(void*, boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:566:22,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<E, A>::~AllocatorStringStorage() [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:648:17,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::reserve(boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:702:5,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::append(ForwardIterator, ForwardIterator) [with ForwardIterator = char*; E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:715:20,  
    inlined from ‘void boost::wave::util::CowString<Storage, Align>::append(FwdIterator, FwdIterator) [with FwdIterator = char*; Storage = boost::wave::util::AllocatorStringStorage<char>; Align = char*]’ at ./boost/wave/util/flex_string.hpp:1374:22,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::push_back(boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1693:24,  
    inlined from ‘boost::wave::util::flex_string<E, T, A, Storage>& boost::wave::util::flex_string<E, T, A, Storage>::operator+=(boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1639:18,  
    inlined from ‘boost::wave::token_id boost::wave::cpplexer::re2clex::scan(boost::wave::cpplexer::re2clex::Scanner<Iterator>*) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >]’ at cpp.re:524:28:  
/usr/include/c++/11/ext/new_allocator.h:145:26: warning: ‘void operator delete(void*, std::size_t)’ called on unallocated object ‘boost::wave::util::SimpleStringStorage<char, std::allocator<char> >::emptyString_’ [-Wfree-nonheap-object]  
  145 |         ::operator delete(__p  
      |         ~~~~~~~~~~~~~~~~~^~~~  
  146 | #if __cpp_sized_deallocation  
      | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                           , __t * sizeof(_Tp)  
      |                           ~~~~~~~~~~~~~~~~~~~  
  148 | #endif  
      | ~~~~~~                      
  149 |                          );  
      |                          ~  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
./boost/wave/util/flex_string.hpp: In function ‘boost::wave::token_id boost::wave::cpplexer::re2clex::scan(boost::wave::cpplexer::re2clex::Scanner<Iterator>*) [with Iterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >]’:  
./boost/wave/util/flex_string.hpp:535:1: note: declared here  
  535 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/x86_64-linux-gnu/c++/11/bits/c++allocator.h:33,  
                 from /usr/include/c++/11/bits/allocator.h:46,  
                 from /usr/include/c++/11/string:41,  
                 from /usr/include/c++/11/bits/locale_classes.h:40,  
                 from /usr/include/c++/11/bits/ios_base.h:41,  
                 from /usr/include/c++/11/streambuf:41,  
                 from /usr/include/c++/11/bits/streambuf_iterator.h:35,  
                 from /usr/include/c++/11/iterator:66,  
                 from ./boost/iterator/iterator_categories.hpp:23,  
                 from ./boost/iterator/iterator_adaptor.hpp:14,  
                 from ./boost/iterator/reverse_iterator.hpp:10,  
                 from ./boost/wave/util/flex_string.hpp:90,  
                 from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
In member function ‘void __gnu_cxx::new_allocator<_Tp>::deallocate(_Tp*, __gnu_cxx::new_allocator<_Tp>::size_type) [with _Tp = char]’,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::Free(void*, boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:566:22,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<E, A>::~AllocatorStringStorage() [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:648:17,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::reserve(boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:702:5,  
    inlined from ‘void boost::wave::util::CowString<Storage, Align>::reserve(boost::wave::util::CowString<Storage, Align>::size_type) [with Storage = boost::wave::util::AllocatorStringStorage<char>; Align = char*]’ at ./boost/wave/util/flex_string.hpp:1381:23,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::reserve(boost::wave::util::flex_string<E, T, A, Storage>::size_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1602:25,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::push_back(boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1691:20,  
    inlined from ‘boost::wave::util::flex_string<E, T, A, Storage>& boost::wave::util::flex_string<E, T, A, Storage>::operator+=(boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1639:18,  
    inlined from ‘boost::wave::token_id boost::wave::cpplexer::re2clex::scan(boost::wave::cpplexer::re2clex::Scanner<Iterator>*) [with Iterator = __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >]’ at cpp.re:524:28:  
/usr/include/c++/11/ext/new_allocator.h:145:26: warning: ‘void operator delete(void*, std::size_t)’ called on unallocated object ‘boost::wave::util::SimpleStringStorage<char, std::allocator<char> >::emptyString_’ [-Wfree-nonheap-object]  
  145 |         ::operator delete(__p  
      |         ~~~~~~~~~~~~~~~~~^~~~  
  146 | #if __cpp_sized_deallocation  
      | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                           , __t * sizeof(_Tp)  
      |                           ~~~~~~~~~~~~~~~~~~~  
  148 | #endif  
      | ~~~~~~                      
  149 |                          );  
      |                          ~  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
./boost/wave/util/flex_string.hpp: In function ‘boost::wave::token_id boost::wave::cpplexer::re2clex::scan(boost::wave::cpplexer::re2clex::Scanner<Iterator>*) [with Iterator = __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >]’:  
./boost/wave/util/flex_string.hpp:535:1: note: declared here  
  535 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/x86_64-linux-gnu/c++/11/bits/c++allocator.h:33,  
                 from /usr/include/c++/11/bits/allocator.h:46,  
                 from /usr/include/c++/11/string:41,  
                 from /usr/include/c++/11/bits/locale_classes.h:40,  
                 from /usr/include/c++/11/bits/ios_base.h:41,  
                 from /usr/include/c++/11/streambuf:41,  
                 from /usr/include/c++/11/bits/streambuf_iterator.h:35,  
                 from /usr/include/c++/11/iterator:66,  
                 from ./boost/iterator/iterator_categories.hpp:23,  
                 from ./boost/iterator/iterator_adaptor.hpp:14,  
                 from ./boost/iterator/reverse_iterator.hpp:10,  
                 from ./boost/wave/util/flex_string.hpp:90,  
                 from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
In member function ‘void __gnu_cxx::new_allocator<_Tp>::deallocate(_Tp*, __gnu_cxx::new_allocator<_Tp>::size_type) [with _Tp = char]’,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::Free(void*, boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:566:22,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<E, A>::~AllocatorStringStorage() [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:648:17,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::reserve(boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:702:5,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::append(ForwardIterator, ForwardIterator) [with ForwardIterator = char*; E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:715:20,  
    inlined from ‘void boost::wave::util::CowString<Storage, Align>::append(FwdIterator, FwdIterator) [with FwdIterator = char*; Storage = boost::wave::util::AllocatorStringStorage<char>; Align = char*]’ at ./boost/wave/util/flex_string.hpp:1374:22,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::push_back(boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1693:24,  
    inlined from ‘boost::wave::util::flex_string<E, T, A, Storage>& boost::wave::util::flex_string<E, T, A, Storage>::operator+=(boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1639:18,  
    inlined from ‘boost::wave::token_id boost::wave::cpplexer::re2clex::scan(boost::wave::cpplexer::re2clex::Scanner<Iterator>*) [with Iterator = __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >]’ at cpp.re:524:28:  
/usr/include/c++/11/ext/new_allocator.h:145:26: warning: ‘void operator delete(void*, std::size_t)’ called on unallocated object ‘boost::wave::util::SimpleStringStorage<char, std::allocator<char> >::emptyString_’ [-Wfree-nonheap-object]  
  145 |         ::operator delete(__p  
      |         ~~~~~~~~~~~~~~~~~^~~~  
  146 | #if __cpp_sized_deallocation  
      | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                           , __t * sizeof(_Tp)  
      |                           ~~~~~~~~~~~~~~~~~~~  
  148 | #endif  
      | ~~~~~~                      
  149 |                          );  
      |                          ~  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
./boost/wave/util/flex_string.hpp: In function ‘boost::wave::token_id boost::wave::cpplexer::re2clex::scan(boost::wave::cpplexer::re2clex::Scanner<Iterator>*) [with Iterator = __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >]’:  
./boost/wave/util/flex_string.hpp:535:1: note: declared here  
  535 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/x86_64-linux-gnu/c++/11/bits/c++allocator.h:33,  
                 from /usr/include/c++/11/bits/allocator.h:46,  
                 from /usr/include/c++/11/string:41,  
                 from /usr/include/c++/11/bits/locale_classes.h:40,  
                 from /usr/include/c++/11/bits/ios_base.h:41,  
                 from /usr/include/c++/11/streambuf:41,  
                 from /usr/include/c++/11/bits/streambuf_iterator.h:35,  
                 from /usr/include/c++/11/iterator:66,  
                 from ./boost/iterator/iterator_categories.hpp:23,  
                 from ./boost/iterator/iterator_adaptor.hpp:14,  
                 from ./boost/iterator/reverse_iterator.hpp:10,  
                 from ./boost/wave/util/flex_string.hpp:90,  
                 from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
In member function ‘void __gnu_cxx::new_allocator<_Tp>::deallocate(_Tp*, __gnu_cxx::new_allocator<_Tp>::size_type) [with _Tp = char]’,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::Free(void*, boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:566:22,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<E, A>::~AllocatorStringStorage() [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:648:17,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::reserve(boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:702:5,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::resize(boost::wave::util::AllocatorStringStorage<E, A>::size_type, E) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:676:16,  
    inlined from ‘void boost::wave::util::CowString<Storage, Align>::resize(boost::wave::util::CowString<Storage, Align>::size_type, boost::wave::util::CowString<Storage, Align>::E) [with Storage = boost::wave::util::AllocatorStringStorage<char>; Align = char*]’ at ./boost/wave/util/flex_string.hpp:1367:22,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::resize(boost::wave::util::flex_string<E, T, A, Storage>::size_type, boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1591:22,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::resize(boost::wave::util::flex_string<E, T, A, Storage>::size_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1594:13,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::clear() [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1606:13,  
    inlined from ‘TokenT& boost::wave::cpplexer::re2clex::lexer<IteratorT, PositionT, TokenT>::get(TokenT&) [with IteratorT = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; PositionT = boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >; TokenT = boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > > >]’ at ./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:246:20:  
/usr/include/c++/11/ext/new_allocator.h:145:26: warning: ‘void operator delete(void*, std::size_t)’ called on unallocated object ‘boost::wave::util::SimpleStringStorage<char, std::allocator<char> >::emptyString_’ [-Wfree-nonheap-object]  
  145 |         ::operator delete(__p  
      |         ~~~~~~~~~~~~~~~~~^~~~  
  146 | #if __cpp_sized_deallocation  
      | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                           , __t * sizeof(_Tp)  
      |                           ~~~~~~~~~~~~~~~~~~~  
  148 | #endif  
      | ~~~~~~                      
  149 |                          );  
      |                          ~  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
./boost/wave/util/flex_string.hpp: In member function ‘TokenT& boost::wave::cpplexer::re2clex::lexer<IteratorT, PositionT, TokenT>::get(TokenT&) [with IteratorT = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; PositionT = boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >; TokenT = boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > > >]’:  
./boost/wave/util/flex_string.hpp:535:1: note: declared here  
  535 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/x86_64-linux-gnu/c++/11/bits/c++allocator.h:33,  
                 from /usr/include/c++/11/bits/allocator.h:46,  
                 from /usr/include/c++/11/string:41,  
                 from /usr/include/c++/11/bits/locale_classes.h:40,  
                 from /usr/include/c++/11/bits/ios_base.h:41,  
                 from /usr/include/c++/11/streambuf:41,  
                 from /usr/include/c++/11/bits/streambuf_iterator.h:35,  
                 from /usr/include/c++/11/iterator:66,  
                 from ./boost/iterator/iterator_categories.hpp:23,  
                 from ./boost/iterator/iterator_adaptor.hpp:14,  
                 from ./boost/iterator/reverse_iterator.hpp:10,  
                 from ./boost/wave/util/flex_string.hpp:90,  
                 from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
In member function ‘void __gnu_cxx::new_allocator<_Tp>::deallocate(_Tp*, __gnu_cxx::new_allocator<_Tp>::size_type) [with _Tp = char]’,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::Free(void*, boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:566:22,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<E, A>::~AllocatorStringStorage() [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:648:17,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::reserve(boost::wave::util::AllocatorStringStorage<E, A>::size_type) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:702:5,  
    inlined from ‘void boost::wave::util::AllocatorStringStorage<E, A>::resize(boost::wave::util::AllocatorStringStorage<E, A>::size_type, E) [with E = char; A = std::allocator<char>]’ at ./boost/wave/util/flex_string.hpp:676:16,  
    inlined from ‘void boost::wave::util::CowString<Storage, Align>::resize(boost::wave::util::CowString<Storage, Align>::size_type, boost::wave::util::CowString<Storage, Align>::E) [with Storage = boost::wave::util::AllocatorStringStorage<char>; Align = char*]’ at ./boost/wave/util/flex_string.hpp:1367:22,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::resize(boost::wave::util::flex_string<E, T, A, Storage>::size_type, boost::wave::util::flex_string<E, T, A, Storage>::value_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1591:22,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::resize(boost::wave::util::flex_string<E, T, A, Storage>::size_type) [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1594:13,  
    inlined from ‘void boost::wave::util::flex_string<E, T, A, Storage>::clear() [with E = char; T = std::char_traits<char>; A = std::allocator<char>; Storage = boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >]’ at ./boost/wave/util/flex_string.hpp:1606:13,  
    inlined from ‘TokenT& boost::wave::cpplexer::re2clex::lexer<IteratorT, PositionT, TokenT>::get(TokenT&) [with IteratorT = __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >; PositionT = boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >; TokenT = boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > > >]’ at ./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:246:20:  
/usr/include/c++/11/ext/new_allocator.h:145:26: warning: ‘void operator delete(void*, std::size_t)’ called on unallocated object ‘boost::wave::util::SimpleStringStorage<char, std::allocator<char> >::emptyString_’ [-Wfree-nonheap-object]  
  145 |         ::operator delete(__p  
      |         ~~~~~~~~~~~~~~~~~^~~~  
  146 | #if __cpp_sized_deallocation  
      | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                           , __t * sizeof(_Tp)  
      |                           ~~~~~~~~~~~~~~~~~~~  
  148 | #endif  
      | ~~~~~~                      
  149 |                          );  
      |                          ~  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_re2c_lexer_str.cpp:16:  
./boost/wave/util/flex_string.hpp: In member function ‘TokenT& boost::wave::cpplexer::re2clex::lexer<IteratorT, PositionT, TokenT>::get(TokenT&) [with IteratorT = __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >; PositionT = boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >; TokenT = boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > > >]’:  
./boost/wave/util/flex_string.hpp:535:1: note: declared here  
  535 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
gcc.archive bin.v2/libs/wave/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/libboost_wave.a  
boost-install.generate-cmake-variant- bin.v2/libs/wave/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/libboost_wave-variant-static.cmake  
common.copy /home/mona/boost_1_72_0/stage/lib/cmake/boost_wave-1.72.0/libboost_wave-variant-static.cmake  
common.copy /home/mona/boost_1_72_0/stage/lib/libboost_wave.a  
...failed updating 20 targets...  
...skipped 79 targets...  
...updated 1760 targets...  
```  
  
The commands:  
```  
 2002  wget https://boostorg.jfrog.io/artifactory/main/release/1.72.0/source/boost_1_72_0.tar.gz  
 2003  tar xvf boost_1_72_0.tar.gz   
 2004  cd boost_1_72_0/  
 2005  ./bootstrap.sh   
 2006  sudo ./b2  
```

---

## Comment 1

> Username: monajalal  
> Created at: 2023-06-05 19:50:56 UTC  
> Url: https://github.com/boostorg/boost/issues/780#issuecomment-1577380986  

It seems it may have been installed so I am not sure why I got that error above?  
```  
(base) mona@ard-gpu-01:~$ cat boost_2_test.cpp  
#include <boost/version.hpp>  
#include <iostream>  
#include <iomanip>  
  
int main()  
{  
    std::cout << "Boost version: "   
          << BOOST_VERSION / 100000  
          << "."  
          << BOOST_VERSION / 100 % 1000  
          << "."  
          << BOOST_VERSION % 100   
          << std::endl;  
    return 0;  
}  
```  
  
and  
  
```  
(base) mona@ard-gpu-01:~$ g++ boost_2_test.cpp   
```  
and  
```  
(base) mona@ard-gpu-01:~$ ./a.out   
Boost version: 1.72.0  
  
```
