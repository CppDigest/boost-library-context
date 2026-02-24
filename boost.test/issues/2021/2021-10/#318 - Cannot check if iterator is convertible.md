# #318 - Cannot check if iterator is convertible [Closed]

> Username: BenniProbst  
> Created at: 2021-10-05 10:29:50 UTC  
> Updated at: 2022-02-27 01:35:37 UTC  
> Closed at: 2022-02-27 01:35:17 UTC  
> Url: https://github.com/boostorg/test/issues/318  

if I use the testing environment within a compile switch with mp11 over a std::variant I cannot check if the sequence resulting is valid with   
  
containerCreator<std::vector<std::string>,  
            std::deque<std::string>,  
            std::forward_list<std::string>,  
            std::list<std::string>,  
            boost::container::vector<std::string>,  
            boost::container::stable_vector<std::string>,  
            boost::container::devector<std::string>,  
            boost::container::deque<std::string>,  
            boost::container::slist<std::string>,  
            boost::container::list<std::string>> c1;  
    std::vector<int> first{1, 2}, second{3, 4};  
    BOOST_CHECK_EQUAL(c1.createSingle(1),std::vector<std::string>{"1"});  
    std::variant<std::vector<std::string>,  
    std::deque<std::string>,  
    std::forward_list<std::string>,  
    std::list<std::string>,  
    boost::container::vector<std::string>,  
    boost::container::stable_vector<std::string>,  
    boost::container::devector<std::string>,  
    boost::container::deque<std::string>,  
    boost::container::slist<std::string>,  
    boost::container::list<std::string>> var1 = c1.create(first, second.begin(),second.end());  
    std::vector<std::string> v1{"1", "2", "3", "4"};  
    BOOST_CHECK_EQUAL(StringIterator<decltype(v1.begin()->begin())>,1);  
    boost::mp11::mp_with_index<boost::mp11::mp_size<decltype(var1)>>(  
    var1.index(), [&](auto I) {  
        BOOST_CHECK_EQUAL_COLLECTIONS(std::get<I>(var1).begin(), std::get<I>(var1).end(), v1.begin(),  
                                      v1.end());  
    });  
    std::cout << std::endl;  
  
====================[ Build | UltiHash | Debug ]================================  
/usr/local/bin/cmake --build /home/benjamin/CLionProjects/UltiHash/cmake-build-debug --target UltiHash -j 8  
Scanning dependencies of target UltiHash  
[ 11%] Building CXX object CMakeFiles/UltiHash.dir/main.cpp.o  
In file included from /usr/local/include/boost/type_traits/is_convertible.hpp:20,  
                 from /usr/local/include/boost/iterator/interoperable.hpp:13,  
                 from /usr/local/include/boost/iterator/iterator_facade.hpp:11,  
                 from /usr/local/include/boost/filesystem/path.hpp:22,  
                 from /usr/local/include/boost/filesystem.hpp:16,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:19,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp: In instantiation of ‘std::ostream& boost::test_tools::tt_detail::impl::boost_test_print_type(std::ostream&, const T&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:62:45:   required from ‘std::ostream& boost::test_tools::tt_detail::impl::boost_test_print_type_impl::operator()(std::ostream&, const R&) const [with R = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:91:30:   required from ‘void boost::test_tools::tt_detail::print_log_value<T>::operator()(std::ostream&, const T&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:220:25:   required from ‘std::ostream& boost::test_tools::tt_detail::operator<<(std::ostream&, const boost::test_tools::tt_detail::print_helper_t<T>&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/utils/lazy_ostream.hpp:81:29:   required from ‘std::ostream& boost::unit_test::lazy_ostream_impl<PrevType, T, StorageT>::operator()(std::ostream&) const [with PrevType = boost::unit_test::lazy_ostream; T = boost::test_tools::tt_detail::print_helper_t<std::vector<std::__cxx11::basic_string<char> > >; StorageT = const boost::test_tools::tt_detail::print_helper_t<std::vector<std::__cxx11::basic_string<char> > >&; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/utils/lazy_ostream.hpp:79:21:   required from here  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:53:13: error: static assertion failed: Type has to implement operator<< to be printable  
   53 |             BOOST_STATIC_ASSERT_MSG( (boost::has_left_shift<std::ostream,T>::value),  
      |             ^~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:53:13: note: ‘boost::integral_constant<bool, false>::value’ evaluates to false  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: error: no match for ‘operator<<’ (operand types are ‘std::ostream’ {aka ‘std::basic_ostream<char>’} and ‘const std::vector<std::__cxx11::basic_string<char> >’)  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:108:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(std::basic_ostream<_CharT, _Traits>::__ostream_type& (*)(std::basic_ostream<_CharT, _Traits>::__ostream_type&)) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  108 |       operator<<(__ostream_type& (*__pf)(__ostream_type&))  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:108:36: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘std::basic_ostream<char>::__ostream_type& (*)(std::basic_ostream<char>::__ostream_type&)’ {aka ‘std::basic_ostream<char>& (*)(std::basic_ostream<char>&)’}  
  108 |       operator<<(__ostream_type& (*__pf)(__ostream_type&))  
      |                  ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:117:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(std::basic_ostream<_CharT, _Traits>::__ios_type& (*)(std::basic_ostream<_CharT, _Traits>::__ios_type&)) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>; std::basic_ostream<_CharT, _Traits>::__ios_type = std::basic_ios<char>]’  
  117 |       operator<<(__ios_type& (*__pf)(__ios_type&))  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:117:32: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘std::basic_ostream<char>::__ios_type& (*)(std::basic_ostream<char>::__ios_type&)’ {aka ‘std::basic_ios<char>& (*)(std::basic_ios<char>&)’}  
  117 |       operator<<(__ios_type& (*__pf)(__ios_type&))  
      |                  ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:127:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(std::ios_base& (*)(std::ios_base&)) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  127 |       operator<<(ios_base& (*__pf) (ios_base&))  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:127:30: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘std::ios_base& (*)(std::ios_base&)’  
  127 |       operator<<(ios_base& (*__pf) (ios_base&))  
      |                  ~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:166:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(long int) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  166 |       operator<<(long __n)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:166:23: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘long int’  
  166 |       operator<<(long __n)  
      |                  ~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:170:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(long unsigned int) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  170 |       operator<<(unsigned long __n)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:170:32: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘long unsigned int’  
  170 |       operator<<(unsigned long __n)  
      |                  ~~~~~~~~~~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:174:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(bool) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  174 |       operator<<(bool __n)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:174:23: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘bool’  
  174 |       operator<<(bool __n)  
      |                  ~~~~~^~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/ostream:820,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/ostream.tcc:91:5: note: candidate: ‘std::basic_ostream<_CharT, _Traits>& std::basic_ostream<_CharT, _Traits>::operator<<(short int) [with _CharT = char; _Traits = std::char_traits<char>]’  
   91 |     basic_ostream<_CharT, _Traits>::  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/ostream.tcc:92:22: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘short int’  
   92 |     operator<<(short __n)  
      |                ~~~~~~^~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:181:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(short unsigned int) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  181 |       operator<<(unsigned short __n)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:181:33: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘short unsigned int’  
  181 |       operator<<(unsigned short __n)  
      |                  ~~~~~~~~~~~~~~~^~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/ostream:820,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/ostream.tcc:105:5: note: candidate: ‘std::basic_ostream<_CharT, _Traits>& std::basic_ostream<_CharT, _Traits>::operator<<(int) [with _CharT = char; _Traits = std::char_traits<char>]’  
  105 |     basic_ostream<_CharT, _Traits>::  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/ostream.tcc:106:20: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘int’  
  106 |     operator<<(int __n)  
      |                ~~~~^~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:192:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(unsigned int) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  192 |       operator<<(unsigned int __n)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:192:31: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘unsigned int’  
  192 |       operator<<(unsigned int __n)  
      |                  ~~~~~~~~~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:201:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(long long int) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  201 |       operator<<(long long __n)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:201:28: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘long long int’  
  201 |       operator<<(long long __n)  
      |                  ~~~~~~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:205:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(long long unsigned int) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  205 |       operator<<(unsigned long long __n)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:205:37: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘long long unsigned int’  
  205 |       operator<<(unsigned long long __n)  
      |                  ~~~~~~~~~~~~~~~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:220:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(double) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  220 |       operator<<(double __f)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:220:25: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘double’  
  220 |       operator<<(double __f)  
      |                  ~~~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:224:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(float) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  224 |       operator<<(float __f)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:224:24: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘float’  
  224 |       operator<<(float __f)  
      |                  ~~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:232:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(long double) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  232 |       operator<<(long double __f)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:232:30: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘long double’  
  232 |       operator<<(long double __f)  
      |                  ~~~~~~~~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:245:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(const void*) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>]’  
  245 |       operator<<(const void* __p)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:245:30: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘const void*’  
  245 |       operator<<(const void* __p)  
      |                  ~~~~~~~~~~~~^~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:250:7: note: candidate: ‘std::basic_ostream<_CharT, _Traits>::__ostream_type& std::basic_ostream<_CharT, _Traits>::operator<<(std::nullptr_t) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__ostream_type = std::basic_ostream<char>; std::nullptr_t = std::nullptr_t]’  
  250 |       operator<<(nullptr_t)  
      |       ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:250:18: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘std::nullptr_t’  
  250 |       operator<<(nullptr_t)  
      |                  ^~~~~~~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/ostream:820,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/ostream.tcc:119:5: note: candidate: ‘std::basic_ostream<_CharT, _Traits>& std::basic_ostream<_CharT, _Traits>::operator<<(std::basic_ostream<_CharT, _Traits>::__streambuf_type*) [with _CharT = char; _Traits = std::char_traits<char>; std::basic_ostream<_CharT, _Traits>::__streambuf_type = std::basic_streambuf<char>]’  
  119 |     basic_ostream<_CharT, _Traits>::  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/ostream.tcc:120:34: note:   no known conversion for argument 1 from ‘const std::vector<std::__cxx11::basic_string<char> >’ to ‘std::basic_ostream<char>::__streambuf_type*’ {aka ‘std::basic_streambuf<char>*’}  
  120 |     operator<<(__streambuf_type* __sbin)  
      |                ~~~~~~~~~~~~~~~~~~^~~~~~  
In file included from /usr/local/include/boost/filesystem/exception.hpp:20,  
                 from /usr/local/include/boost/filesystem.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:19,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/smart_ptr/intrusive_ptr.hpp:364:64: note: candidate: ‘template<class E, class T, class Y> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::intrusive_ptr<Y>&)’  
  364 | template<class E, class T, class Y> std::basic_ostream<E, T> & operator<< (std::basic_ostream<E, T> & os, intrusive_ptr<Y> const & p)  
      |                                                                ^~~~~~~~  
/usr/local/include/boost/smart_ptr/intrusive_ptr.hpp:364:64: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const boost::intrusive_ptr<Y>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/throw_exception.hpp:24,  
                 from /usr/local/include/boost/property_tree/ptree_fwd.hpp:16,  
                 from /usr/local/include/boost/property_tree/ptree.hpp:15,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:14,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/assert/source_location.hpp:58:55: note: candidate: ‘template<class E, class T> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::source_location&)’  
   58 | template<class E, class T> std::basic_ostream<E, T> & operator<<( std::basic_ostream<E, T> & os, source_location const & loc )  
      |                                                       ^~~~~~~~  
/usr/local/include/boost/assert/source_location.hpp:58:55: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const boost::source_location&’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/optional.hpp:15,  
                 from /usr/local/include/boost/property_tree/id_translator.hpp:16,  
                 from /usr/local/include/boost/property_tree/string_path.hpp:15,  
                 from /usr/local/include/boost/property_tree/ptree.hpp:16,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:14,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/optional/optional.hpp:1594:1: note: candidate: ‘template<class CharType, class CharTrait> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::optional_detail::optional_tag&)’  
 1594 | operator<<(std::basic_ostream<CharType, CharTrait>& os, optional_detail::optional_tag const&)  
      | ^~~~~~~~  
/usr/local/include/boost/optional/optional.hpp:1594:1: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const boost::optional_detail::optional_tag&’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/stream_translator.hpp:17,  
                 from /usr/local/include/boost/property_tree/ptree.hpp:17,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:14,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/optional/optional_io.hpp:28:1: note: candidate: ‘template<class CharType, class CharTrait> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, boost::none_t)’  
   28 | operator<<(std::basic_ostream<CharType, CharTrait>& out, none_t)  
      | ^~~~~~~~  
/usr/local/include/boost/optional/optional_io.hpp:28:1: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘boost::none_t’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/stream_translator.hpp:17,  
                 from /usr/local/include/boost/property_tree/ptree.hpp:17,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:14,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/optional/optional_io.hpp:41:1: note: candidate: ‘template<class CharType, class CharTrait, class T> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::optional<T>&)’  
   41 | operator<<(std::basic_ostream<CharType, CharTrait>& out, optional<T> const& v)  
      | ^~~~~~~~  
/usr/local/include/boost/optional/optional_io.hpp:41:1: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const boost::optional<T>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/shared_ptr.hpp:17,  
                 from /usr/local/include/boost/format/alt_sstream.hpp:22,  
                 from /usr/local/include/boost/format/internals.hpp:24,  
                 from /usr/local/include/boost/format.hpp:38,  
                 from /usr/local/include/boost/property_tree/json_parser/detail/parser.hpp:8,  
                 from /usr/local/include/boost/property_tree/json_parser/detail/read.hpp:13,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:16,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/smart_ptr/shared_ptr.hpp:1058:64: note: candidate: ‘template<class E, class T, class Y> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::shared_ptr<Y>&)’  
 1058 | template<class E, class T, class Y> std::basic_ostream<E, T> & operator<< (std::basic_ostream<E, T> & os, shared_ptr<Y> const & p)  
      |                                                                ^~~~~~~~  
/usr/local/include/boost/smart_ptr/shared_ptr.hpp:1058:64: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const boost::shared_ptr<Y>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/format.hpp:53,  
                 from /usr/local/include/boost/property_tree/json_parser/detail/parser.hpp:8,  
                 from /usr/local/include/boost/property_tree/json_parser/detail/read.hpp:13,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:16,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/format/free_funcs.hpp:33:9: note: candidate: ‘template<class Ch, class Tr, class Alloc> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::basic_format<Ch, Tr, Alloc>&)’  
   33 |         operator<<( std::basic_ostream<Ch, Tr> & os,  
      |         ^~~~~~~~  
/usr/local/include/boost/format/free_funcs.hpp:33:9: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const boost::basic_format<Ch, Tr, Alloc>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/range/iterator_range.hpp:14,  
                 from /usr/local/include/boost/range/algorithm.hpp:30,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:21,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/range/iterator_range_io.hpp:55:49: note: candidate: ‘template<class IteratorT, class Elem, class Traits> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::iterator_range<IteratorT>&)’  
   55 |         inline std::basic_ostream<Elem,Traits>& operator<<(  
      |                                                 ^~~~~~~~  
/usr/local/include/boost/range/iterator_range_io.hpp:55:49: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const boost::iterator_range<IteratorT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/function_types/parameter_types.hpp:12,  
                 from /usr/local/include/boost/tti/detail/dftclass.hpp:10,  
                 from /usr/local/include/boost/tti/detail/dmem_data.hpp:19,  
                 from /usr/local/include/boost/tti/detail/ddata.hpp:12,  
                 from /usr/local/include/boost/tti/has_data.hpp:14,  
                 from /usr/local/include/boost/tti/tti.hpp:11,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:28,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/blank.hpp:93:46: note: candidate: ‘template<class E, class T> std::basic_ostream<_CharT, _Traits>& boost::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::blank&)’  
   93 | inline BOOST_TEMPLATED_STREAM(ostream, E,T)& operator<<(  
      |                                              ^~~~~~~~  
/usr/local/include/boost/blank.hpp:93:46: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const boost::blank&’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/test/tools/assertion_result.hpp:17,  
                 from /usr/local/include/boost/test/tree/decorator.hpp:24,  
                 from /usr/local/include/boost/test/tree/test_unit.hpp:21,  
                 from /usr/local/include/boost/test/impl/compiler_log_formatter.ipp:25,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:18,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:64:1: note: candidate: ‘template<class CharT, class T> boost::basic_wrap_stringstream<CharT>& boost::operator<<(boost::basic_wrap_stringstream<CharT>&, const T&)’  
   64 | operator<<( basic_wrap_stringstream<CharT>& targ, T const& t )  
      | ^~~~~~~~  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:64:1: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::ostream’ {aka ‘std::basic_ostream<char>’} is not derived from ‘boost::basic_wrap_stringstream<CharT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/test/tools/assertion_result.hpp:17,  
                 from /usr/local/include/boost/test/tree/decorator.hpp:24,  
                 from /usr/local/include/boost/test/tree/test_unit.hpp:21,  
                 from /usr/local/include/boost/test/impl/compiler_log_formatter.ipp:25,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:18,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:109:1: note: candidate: ‘template<class CharT> boost::basic_wrap_stringstream<CharT>& boost::operator<<(boost::basic_wrap_stringstream<CharT>&, boost::basic_wrap_stringstream<CharT>&)’  
  109 | operator<<( basic_wrap_stringstream<CharT>& targ, basic_wrap_stringstream<CharT>& src )  
      | ^~~~~~~~  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:109:1: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::ostream’ {aka ‘std::basic_ostream<char>’} is not derived from ‘boost::basic_wrap_stringstream<CharT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/test/tools/assertion_result.hpp:17,  
                 from /usr/local/include/boost/test/tree/decorator.hpp:24,  
                 from /usr/local/include/boost/test/tree/test_unit.hpp:21,  
                 from /usr/local/include/boost/test/impl/compiler_log_formatter.ipp:25,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:18,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:121:1: note: candidate: ‘template<class CharT> boost::basic_wrap_stringstream<CharT>& boost::operator<<(boost::basic_wrap_stringstream<CharT>&, std::ios_base& (*)(std::ios_base&))’  
  121 | operator<<( basic_wrap_stringstream<CharT>& targ, std::ios_base& (BOOST_TEST_CALL_DECL *man)(std::ios_base&) )  
      | ^~~~~~~~  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:121:1: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::ostream’ {aka ‘std::basic_ostream<char>’} is not derived from ‘boost::basic_wrap_stringstream<CharT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/test/tools/assertion_result.hpp:17,  
                 from /usr/local/include/boost/test/tree/decorator.hpp:24,  
                 from /usr/local/include/boost/test/tree/test_unit.hpp:21,  
                 from /usr/local/include/boost/test/impl/compiler_log_formatter.ipp:25,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:18,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:131:1: note: candidate: ‘template<class CharT, class Elem, class Tr> boost::basic_wrap_stringstream<CharT>& boost::operator<<(boost::basic_wrap_stringstream<CharT>&, std::basic_ostream<_CharT, _Traits>& (*)(std::basic_ostream<_CharT, _Traits>&))’  
  131 | operator<<( basic_wrap_stringstream<CharT>& targ, std::basic_ostream<Elem,Tr>& (BOOST_TEST_CALL_DECL *man)(std::basic_ostream<Elem, Tr>&) )  
      | ^~~~~~~~  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:131:1: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::ostream’ {aka ‘std::basic_ostream<char>’} is not derived from ‘boost::basic_wrap_stringstream<CharT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/test/tools/assertion_result.hpp:17,  
                 from /usr/local/include/boost/test/tree/decorator.hpp:24,  
                 from /usr/local/include/boost/test/tree/test_unit.hpp:21,  
                 from /usr/local/include/boost/test/impl/compiler_log_formatter.ipp:25,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:18,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:141:1: note: candidate: ‘template<class CharT, class Elem, class Tr> boost::basic_wrap_stringstream<CharT>& boost::operator<<(boost::basic_wrap_stringstream<CharT>&, std::basic_ios<Elem, Tr>& (*)(std::basic_ios<Elem, Tr>&))’  
  141 | operator<<( basic_wrap_stringstream<CharT>& targ, std::basic_ios<Elem, Tr>& (BOOST_TEST_CALL_DECL *man)(std::basic_ios<Elem, Tr>&) )  
      | ^~~~~~~~  
/usr/local/include/boost/test/utils/wrap_stringstream.hpp:141:1: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::ostream’ {aka ‘std::basic_ostream<char>’} is not derived from ‘boost::basic_wrap_stringstream<CharT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/exception/diagnostic_information.hpp:11,  
                 from /usr/local/include/boost/test/impl/execution_monitor.ipp:37,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:23,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/exception/info.hpp:260:5: note: candidate: ‘template<class E, class Tag, class T> typename boost::enable_if<boost::exception_detail::derives_boost_exception<E>, const E&>::type boost::operator<<(const E&, const boost::error_info<Tag, T>&)’  
  260 |     operator<<( E const & x, error_info<Tag,T> const & v )  
      |     ^~~~~~~~  
/usr/local/include/boost/exception/info.hpp:260:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const boost::error_info<Tag, T>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/exception/diagnostic_information.hpp:11,  
                 from /usr/local/include/boost/test/impl/execution_monitor.ipp:37,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:23,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/exception/info.hpp:269:5: note: candidate: ‘template<class E, class Tag, class T> typename boost::enable_if<boost::exception_detail::derives_boost_exception<E>, const E&>::type boost::operator<<(const E&, boost::error_info<Tag, T>&&)’  
  269 |     operator<<( E const & x, error_info<Tag,T> && v )  
      |     ^~~~~~~~  
/usr/local/include/boost/exception/info.hpp:269:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   types ‘boost::error_info<Tag, T>’ and ‘const std::vector<std::__cxx11::basic_string<char> >’ have incompatible cv-qualifiers  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/bits/basic_string.h:48,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/string:55,  
                 from /home/benjamin/CLionProjects/UltiHash/exeptions.h:6,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/string_view:667:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::basic_string_view<_CharT, _Traits>)’  
  667 |     operator<<(basic_ostream<_CharT, _Traits>& __os,  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/string_view:667:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘std::basic_string_view<_CharT, _Traits>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/string:55,  
                 from /home/benjamin/CLionProjects/UltiHash/exeptions.h:6,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/basic_string.h:3591:5: note: candidate: ‘template<class _CharT, class _Traits, class _Alloc> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const std::__cxx11::basic_string<_CharT, _Traits, _Allocator>&)’  
 3591 |     operator<<(basic_ostream<_CharT, _Traits>& __os,  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/basic_string.h:3591:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const std::__cxx11::basic_string<_CharT, _Traits, _Allocator>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/bits/ios_base.h:46,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/ios:42,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/istream:38,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/system_error:279:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const std::error_code&)’  
  279 |     operator<<(basic_ostream<_CharT, _Traits>& __os, const error_code& __e)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/system_error:279:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const std::error_code&’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:500:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, _CharT)’  
  500 |     operator<<(basic_ostream<_CharT, _Traits>& __out, _CharT __c)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:500:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   deduced conflicting types for parameter ‘_CharT’ (‘char’ and ‘std::vector<std::__cxx11::basic_string<char> >’)  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:505:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, char)’  
  505 |     operator<<(basic_ostream<_CharT, _Traits>& __out, char __c)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:505:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:511:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, char)’  
  511 |     operator<<(basic_ostream<char, _Traits>& __out, char __c)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:511:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:517:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, signed char)’  
  517 |     operator<<(basic_ostream<char, _Traits>& __out, signed char __c)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:517:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘signed char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:522:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, unsigned char)’  
  522 |     operator<<(basic_ostream<char, _Traits>& __out, unsigned char __c)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:522:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘unsigned char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:532:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, wchar_t)’ (deleted)  
  532 |     operator<<(basic_ostream<char, _Traits>&, wchar_t) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:532:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘wchar_t’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:538:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, char8_t)’ (deleted)  
  538 |     operator<<(basic_ostream<char, _Traits>&, char8_t) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:538:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘char8_t’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:543:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, char16_t)’ (deleted)  
  543 |     operator<<(basic_ostream<char, _Traits>&, char16_t) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:543:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘char16_t’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:547:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, char32_t)’ (deleted)  
  547 |     operator<<(basic_ostream<char, _Traits>&, char32_t) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:547:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘char32_t’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:553:5: note: candidate: ‘template<class _Traits> std::basic_ostream<wchar_t, _Traits>& std::operator<<(std::basic_ostream<wchar_t, _Traits>&, char8_t)’ (deleted)  
  553 |     operator<<(basic_ostream<wchar_t, _Traits>&, char8_t) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:553:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   mismatched types ‘wchar_t’ and ‘char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:558:5: note: candidate: ‘template<class _Traits> std::basic_ostream<wchar_t, _Traits>& std::operator<<(std::basic_ostream<wchar_t, _Traits>&, char16_t)’ (deleted)  
  558 |     operator<<(basic_ostream<wchar_t, _Traits>&, char16_t) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:558:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   mismatched types ‘wchar_t’ and ‘char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:562:5: note: candidate: ‘template<class _Traits> std::basic_ostream<wchar_t, _Traits>& std::operator<<(std::basic_ostream<wchar_t, _Traits>&, char32_t)’ (deleted)  
  562 |     operator<<(basic_ostream<wchar_t, _Traits>&, char32_t) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:562:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   mismatched types ‘wchar_t’ and ‘char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:583:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const _CharT*)’  
  583 |     operator<<(basic_ostream<_CharT, _Traits>& __out, const _CharT* __s)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:583:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   mismatched types ‘const _CharT*’ and ‘std::vector<std::__cxx11::basic_string<char> >’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/ostream:820,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/ostream.tcc:302:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const char*)’  
  302 |     operator<<(basic_ostream<_CharT, _Traits>& __out, const char* __s)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/ostream.tcc:302:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const char*’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:600:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, const char*)’  
  600 |     operator<<(basic_ostream<char, _Traits>& __out, const char* __s)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:600:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const char*’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:613:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, const signed char*)’  
  613 |     operator<<(basic_ostream<char, _Traits>& __out, const signed char* __s)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:613:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const signed char*’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:618:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, const unsigned char*)’  
  618 |     operator<<(basic_ostream<char, _Traits>& __out, const unsigned char* __s)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:618:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const unsigned char*’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:628:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, const wchar_t*)’ (deleted)  
  628 |     operator<<(basic_ostream<char, _Traits>&, const wchar_t*) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:628:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const wchar_t*’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:634:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, const char8_t*)’ (deleted)  
  634 |     operator<<(basic_ostream<char, _Traits>&, const char8_t*) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:634:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const char8_t*’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:639:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, const char16_t*)’ (deleted)  
  639 |     operator<<(basic_ostream<char, _Traits>&, const char16_t*) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:639:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const char16_t*’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:643:5: note: candidate: ‘template<class _Traits> std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, const char32_t*)’ (deleted)  
  643 |     operator<<(basic_ostream<char, _Traits>&, const char32_t*) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:643:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘const char32_t*’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:649:5: note: candidate: ‘template<class _Traits> std::basic_ostream<wchar_t, _Traits>& std::operator<<(std::basic_ostream<wchar_t, _Traits>&, const char8_t*)’ (deleted)  
  649 |     operator<<(basic_ostream<wchar_t, _Traits>&, const char8_t*) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:649:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   mismatched types ‘wchar_t’ and ‘char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:654:5: note: candidate: ‘template<class _Traits> std::basic_ostream<wchar_t, _Traits>& std::operator<<(std::basic_ostream<wchar_t, _Traits>&, const char16_t*)’ (deleted)  
  654 |     operator<<(basic_ostream<wchar_t, _Traits>&, const char16_t*) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:654:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   mismatched types ‘wchar_t’ and ‘char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:658:5: note: candidate: ‘template<class _Traits> std::basic_ostream<wchar_t, _Traits>& std::operator<<(std::basic_ostream<wchar_t, _Traits>&, const char32_t*)’ (deleted)  
  658 |     operator<<(basic_ostream<wchar_t, _Traits>&, const char32_t*) = delete;  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:658:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   mismatched types ‘wchar_t’ and ‘char’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/istream:39,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/fstream:38,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:741:5: note: candidate: ‘template<class _Ostream, class _Tp> _Ostream&& std::operator<<(_Ostream&&, const _Tp&)’  
  741 |     operator<<(_Ostream&& __os, const _Tp& __x)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:741:5: note:   template argument deduction/substitution failed:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream: In substitution of ‘template<class _Ostream, class _Tp> _Ostream&& std::operator<<(_Ostream&&, const _Tp&) [with _Ostream = std::basic_ostream<char>&; _Tp = std::vector<std::__cxx11::basic_string<char> >]’:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18:   required from ‘std::ostream& boost::test_tools::tt_detail::impl::boost_test_print_type(std::ostream&, const T&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:62:45:   required from ‘std::ostream& boost::test_tools::tt_detail::impl::boost_test_print_type_impl::operator()(std::ostream&, const R&) const [with R = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:91:30:   required from ‘void boost::test_tools::tt_detail::print_log_value<T>::operator()(std::ostream&, const T&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:220:25:   required from ‘std::ostream& boost::test_tools::tt_detail::operator<<(std::ostream&, const boost::test_tools::tt_detail::print_helper_t<T>&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/utils/lazy_ostream.hpp:81:29:   required from ‘std::ostream& boost::unit_test::lazy_ostream_impl<PrevType, T, StorageT>::operator()(std::ostream&) const [with PrevType = boost::unit_test::lazy_ostream; T = boost::test_tools::tt_detail::print_helper_t<std::vector<std::__cxx11::basic_string<char> > >; StorageT = const boost::test_tools::tt_detail::print_helper_t<std::vector<std::__cxx11::basic_string<char> > >&; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/utils/lazy_ostream.hpp:79:21:   required from here  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:741:5: error: template constraint failure for ‘template<class _Os, class _Tp>  requires (__derived_from_ios_base<_Os>) && requires(_Os& __os, const _Tp& __t) {__os << __t;} using __rvalue_stream_insertion_t = _Os&&’  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:741:5: note: constraints not satisfied  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream: In substitution of ‘template<class _Os, class _Tp>  requires (__derived_from_ios_base<_Os>) && requires(_Os& __os, const _Tp& __t) {__os << __t;} using __rvalue_stream_insertion_t = _Os&& [with _Os = std::basic_ostream<char>&; _Tp = std::vector<std::__cxx11::basic_string<char> >]’:  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:741:5:   required by substitution of ‘template<class _Ostream, class _Tp> _Ostream&& std::operator<<(_Ostream&&, const _Tp&) [with _Ostream = std::basic_ostream<char>&; _Tp = std::vector<std::__cxx11::basic_string<char> >]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18:   required from ‘std::ostream& boost::test_tools::tt_detail::impl::boost_test_print_type(std::ostream&, const T&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:62:45:   required from ‘std::ostream& boost::test_tools::tt_detail::impl::boost_test_print_type_impl::operator()(std::ostream&, const R&) const [with R = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:91:30:   required from ‘void boost::test_tools::tt_detail::print_log_value<T>::operator()(std::ostream&, const T&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:220:25:   required from ‘std::ostream& boost::test_tools::tt_detail::operator<<(std::ostream&, const boost::test_tools::tt_detail::print_helper_t<T>&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/utils/lazy_ostream.hpp:81:29:   required from ‘std::ostream& boost::unit_test::lazy_ostream_impl<PrevType, T, StorageT>::operator()(std::ostream&) const [with PrevType = boost::unit_test::lazy_ostream; T = boost::test_tools::tt_detail::print_helper_t<std::vector<std::__cxx11::basic_string<char> > >; StorageT = const boost::test_tools::tt_detail::print_helper_t<std::vector<std::__cxx11::basic_string<char> > >&; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/utils/lazy_ostream.hpp:79:21:   required from here  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:708:13:   required for the satisfaction of ‘__derived_from_ios_base<_Os>’ [with _Os = std::basic_ostream<char, std::char_traits<char> >&]  
/usr/local/gcc-11.2/include/c++/12.0.0/ostream:708:39: note: the expression ‘is_class_v<_Tp> [with _Tp = std::basic_ostream<char, std::char_traits<char> >&]’ evaluated to ‘false’  
  708 |     concept __derived_from_ios_base = is_class_v<_Tp>  
      |                                       ^~~~~~~~~~~~~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/bits/std_thread.h:44,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/stop_token:35,  
                 from /usr/local/gcc-11.2/include/c++/12.0.0/thread:40,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:8,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp: In instantiation of ‘std::ostream& boost::test_tools::tt_detail::impl::boost_test_print_type(std::ostream&, const T&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:62:45:   required from ‘std::ostream& boost::test_tools::tt_detail::impl::boost_test_print_type_impl::operator()(std::ostream&, const R&) const [with R = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:91:30:   required from ‘void boost::test_tools::tt_detail::print_log_value<T>::operator()(std::ostream&, const T&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:220:25:   required from ‘std::ostream& boost::test_tools::tt_detail::operator<<(std::ostream&, const boost::test_tools::tt_detail::print_helper_t<T>&) [with T = std::vector<std::__cxx11::basic_string<char> >; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/utils/lazy_ostream.hpp:81:29:   required from ‘std::ostream& boost::unit_test::lazy_ostream_impl<PrevType, T, StorageT>::operator()(std::ostream&) const [with PrevType = boost::unit_test::lazy_ostream; T = boost::test_tools::tt_detail::print_helper_t<std::vector<std::__cxx11::basic_string<char> > >; StorageT = const boost::test_tools::tt_detail::print_helper_t<std::vector<std::__cxx11::basic_string<char> > >&; std::ostream = std::basic_ostream<char>]’  
/usr/local/include/boost/test/utils/lazy_ostream.hpp:79:21:   required from here  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/unique_ptr.h:1053:5: note: candidate: ‘template<class _CharT, class _Traits, class _Tp, class _Dp> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const std::unique_ptr<_Up, _Ep>&) requires requires{std::operator<<::__os << std::operator<<::__p->get();}’  
 1053 |     operator<<(basic_ostream<_CharT, _Traits>& __os,  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/unique_ptr.h:1053:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const std::unique_ptr<_Up, _Ep>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:8,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/thread:92:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::thread::id)’  
   92 |     operator<<(basic_ostream<_CharT, _Traits>& __out, thread::id __id)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/thread:92:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘std::thread::id’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/config/compiler/gcc.hpp:165,  
                 from /usr/local/include/boost/config.hpp:39,  
                 from /usr/local/include/boost/filesystem/config.hpp:19,  
                 from /usr/local/include/boost/filesystem.hpp:15,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:19,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/cstddef:125:5: note: candidate: ‘template<class _IntegerType> constexpr std::__byte_op_t<_IntegerType> std::operator<<(std::byte, _IntegerType)’  
  125 |     operator<<(byte __b, _IntegerType __shift) noexcept  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/cstddef:125:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘ostr’ (type ‘std::ostream’ {aka ‘std::basic_ostream<char>’}) to type ‘std::byte’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/gcc-11.2/include/c++/12.0.0/memory:77,  
                 from /usr/local/include/boost/property_tree/ptree_fwd.hpp:18,  
                 from /usr/local/include/boost/property_tree/ptree.hpp:15,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:14,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/shared_ptr.h:70:5: note: candidate: ‘template<class _Ch, class _Tr, class _Tp, __gnu_cxx::_Lock_policy _Lp> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const std::__shared_ptr<_Tp, _Lp>&)’  
   70 |     operator<<(std::basic_ostream<_Ch, _Tr>& __os,  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/bits/shared_ptr.h:70:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const std::__shared_ptr<_Tp, _Lp>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/json_parser/detail/write.hpp:18,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:79:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::_Resetiosflags)’  
   79 |     operator<<(basic_ostream<_CharT, _Traits>& __os, _Resetiosflags __f)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:79:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘std::_Resetiosflags’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/json_parser/detail/write.hpp:18,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:109:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::_Setiosflags)’  
  109 |     operator<<(basic_ostream<_CharT, _Traits>& __os, _Setiosflags __f)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:109:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘std::_Setiosflags’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/json_parser/detail/write.hpp:18,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:143:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::_Setbase)’  
  143 |     operator<<(basic_ostream<_CharT, _Traits>& __os, _Setbase __f)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:143:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘std::_Setbase’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/json_parser/detail/write.hpp:18,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:178:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::_Setfill<_CharT>)’  
  178 |     operator<<(basic_ostream<_CharT, _Traits>& __os, _Setfill<_CharT> __f)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:178:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘std::_Setfill<_CharT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/json_parser/detail/write.hpp:18,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:208:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::_Setprecision)’  
  208 |     operator<<(basic_ostream<_CharT, _Traits>& __os, _Setprecision __f)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:208:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘std::_Setprecision’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/json_parser/detail/write.hpp:18,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:238:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::_Setw)’  
  238 |     operator<<(basic_ostream<_CharT, _Traits>& __os, _Setw __f)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:238:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   cannot convert ‘t’ (type ‘const std::vector<std::__cxx11::basic_string<char> >’) to type ‘std::_Setw’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/json_parser/detail/write.hpp:18,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:311:5: note: candidate: ‘template<class _CharT, class _Traits, class _MoneyT> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::_Put_money<_MoneyT>)’  
  311 |     operator<<(basic_ostream<_CharT, _Traits>& __os, _Put_money<_MoneyT> __f)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:311:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘std::_Put_money<_MoneyT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/property_tree/json_parser/detail/write.hpp:18,  
                 from /usr/local/include/boost/property_tree/json_parser.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:20,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:363:5: note: candidate: ‘template<class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, std::_Put_time<_CharT>)’  
  363 |     operator<<(basic_ostream<_CharT, _Traits>& __os, _Put_time<_CharT> __f)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/iomanip:363:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘std::_Put_time<_CharT>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/detail/container_fwd.hpp:96,  
                 from /usr/local/include/boost/container_hash/extensions.hpp:22,  
                 from /usr/local/include/boost/container_hash/hash.hpp:761,  
                 from /usr/local/include/boost/functional/hash.hpp:6,  
                 from /usr/local/include/boost/unordered/unordered_map.hpp:18,  
                 from /usr/local/include/boost/unordered_map.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:43,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/bitset:1540:5: note: candidate: ‘template<class _CharT, class _Traits, long unsigned int _Nb> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const std::bitset<_Nb>&)’  
 1540 |     operator<<(std::basic_ostream<_CharT, _Traits>& __os,  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/bitset:1540:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const std::bitset<_Nb>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
In file included from /usr/local/include/boost/detail/container_fwd.hpp:98,  
                 from /usr/local/include/boost/container_hash/extensions.hpp:22,  
                 from /usr/local/include/boost/container_hash/hash.hpp:761,  
                 from /usr/local/include/boost/functional/hash.hpp:6,  
                 from /usr/local/include/boost/unordered/unordered_map.hpp:18,  
                 from /usr/local/include/boost/unordered_map.hpp:17,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes.h:43,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:5,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/gcc-11.2/include/c++/12.0.0/complex:555:5: note: candidate: ‘template<class _Tp, class _CharT, class _Traits> std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const std::complex<_Tp>&)’  
  555 |     operator<<(basic_ostream<_CharT, _Traits>& __os, const complex<_Tp>& __x)  
      |     ^~~~~~~~  
/usr/local/gcc-11.2/include/c++/12.0.0/complex:555:5: note:   template argument deduction/substitution failed:  
In file included from /usr/local/include/boost/test/tools/old/impl.hpp:24,  
                 from /usr/local/include/boost/test/test_tools.hpp:46,  
                 from /usr/local/include/boost/test/impl/test_tools.ipp:19,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:29,  
                 from /home/benjamin/CLionProjects/UltiHash/conceptTypes_basic_tests.h:7,  
                 from /home/benjamin/CLionProjects/UltiHash/main.cpp:1:  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:55:18: note:   ‘const std::vector<std::__cxx11::basic_string<char> >’ is not derived from ‘const std::complex<_Tp>’  
   55 |             ostr << t;  
      |             ~~~~~^~~~  
make[3]: *** [CMakeFiles/UltiHash.dir/build.make:72: CMakeFiles/UltiHash.dir/main.cpp.o] Error 1  
make[2]: *** [CMakeFiles/Makefile2:83: CMakeFiles/UltiHash.dir/all] Error 2  
make[1]: *** [CMakeFiles/Makefile2:90: CMakeFiles/UltiHash.dir/rule] Error 2  
make: *** [Makefile:124: UltiHash] Error 2

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-02-27 01:35:16 UTC  
> Url: https://github.com/boostorg/test/issues/318#issuecomment-1052917011  

It says   
```  
/usr/local/include/boost/test/tools/detail/print_helper.hpp:53:13: error: static assertion failed: Type has to implement operator<< to be printable  
```  
The documentation related to the types and their printing is [here](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/test_output/test_tools_support_for_logging/testing_tool_output_disable.html).   
You can mark a type non printable if needed: see [here](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/test_output/test_tools_support_for_logging/testing_tool_output_disable.html#boost_test.test_output.test_tools_support_for_logging.testing_tool_output_disable.prohibiting_the_printing_of_a_sp).  
  
Hopes this helps.
