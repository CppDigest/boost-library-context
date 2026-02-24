# #839 - boost::spirit::istream_iterator doesn't support the concept of `std::forward_iterator` [Closed]

> Username: marc-viala-ametek  
> Created at: 2025-11-27 16:40:16 UTC  
> Updated at: 2025-11-29 01:14:38 UTC  
> Closed at: 2025-11-29 01:14:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/839  

I would like to use the library Boost.Parser and Boost.Spirit. The function `boost::parser::prefix_parser` requires that input iterator models `std::forward` but `boost::spirit::istream_iterator` doesn't meet this requirement.  
  
The following code snippet reproduce the error:  
  
``` C++  
#define BOOST_TEST_MODULE inprogress_test_suite  
#include <boost/test/unit_test.hpp>  
  
#include <sstream>  
  
#include <boost/parser/parser.hpp>  
#include <boost/spirit/include/support_istream_iterator.hpp>  
  
namespace app {  
  /////////////////////////////////////////////////////////////////////////////  
  struct xyz_t {  
    struct elem_t {  
      double x;  
      double y;  
      double z;  
    };  
    union {  
      elem_t elem;  
      double values[3];  
    };  
    xyz_t(  
        const double x = 0.0  
      , const double y = 0.0  
      , const double z = 0.0)  
      : values{x, y, z}  
    {}  
  };  
  
  /////////////////////////////////////////////////////////////////////////////  
  struct istream_iterator: boost::spirit::istream_iterator {  
    using super = boost::spirit::istream_iterator;  
    using super::super;  
  
    istream_iterator& operator++()  
    {  
      super::operator++();  
      return *this;  
    }  
  
    istream_iterator operator++(int)  
    {  
      istream_iterator res;  
      super::operator++();  
      return res;  
    }  
  };  
  
  /////////////////////////////////////////////////////////////////////////////  
  std::istream& operator>>(std::istream& is, xyz_t& xyz)  
  {  
    namespace bp = boost::parser;  
    const auto ax{  
      bp::double_[  
        ([x = std::ref(xyz.elem.x)](auto& ctx){ x.get() = _attr(ctx); })]};  
    const auto ay{  
      bp::double_[  
        ([y = std::ref(xyz.elem.y)](auto& ctx){ y.get() = _attr(ctx); })]};  
    const auto az{  
      bp::double_[  
        ([z = std::ref(xyz.elem.z)](auto& ctx){ z.get() = _attr(ctx); })]};  
  
    istream_iterator begin{is}, end{};  
    const auto parser{'(' >> ax >> ',' >> ay >> ',' >> az >> ')'};  
    const auto oc{bp::prefix_parse(begin, end, parser, bp::ws)};  
  
    if(!oc) {  
      is.setstate(std::ios::failbit);  
      return is;  
    }  
  
    return is;  
  }  
}  
  
BOOST_AUTO_TEST_CASE(test)  
{  
  std::stringstream ss;  
  ss << "(1.0,2.0,3.0)";  
  
  app::xyz_t xyz;  
  ss >> xyz;  
  
  BOOST_TEST(xyz.values[0] == 1.0);  
  BOOST_TEST(xyz.values[1] == 2.0);  
  BOOST_TEST(xyz.values[2] == 3.0);  
  BOOST_TEST(ss.fail() == false);  
}  
```  
  
This code compile and the TU is passed. But If we change the type of iterators `begin` & `end` to `boost::spirit::istream_iterator`, we get a compilation error:  
  
```  
1>D:\repos\in-progress\Program\in-progress\.trash\main.cpp(64,23): error C2672: 'boost::parser::prefix_parse' : fonction correspondante surchargée introuvable  
1>    D:\repos\in-progress\include\boost\parser\parser.hpp(9009,10):  
1>    est peut-être 'auto boost::parser::prefix_parse(I &,S,const boost::parser::parser_interface<Parser,GlobalState,ErrorHandler> &,const boost::parser::parser_interface<SkipParser,boost::parser::detail::nope,boost::parser::default_error_handler> &,boost::parser::trace)'  
1>        D:\repos\in-progress\Program\in-progress\.trash\main.cpp(64,23):  
1>        les contraintes associées ne sont pas remplies  
1>            D:\repos\in-progress\include\boost\parser\parser.hpp(8991,9):  
1>            le concept 'boost::parser::parsable_iter<boost::spirit::istream_iterator>' a la valeur false.  
1>                D:\repos\in-progress\include\boost\parser\concepts.hpp(31,9):  
1>                le concept 'std::forward_iterator<boost::spirit::istream_iterator>' a la valeur false.  
1>                    C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\xutility(993,28):  
1>                    le concept 'std::input_iterator<boost::spirit::istream_iterator>' a la valeur false.  
1>                        C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\xutility(984,26):  
1>                        le concept 'std::input_or_output_iterator<boost::spirit::istream_iterator>' a la valeur false.  
1>                            C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\__msvc_iter_core.hpp(404,6):  
1>                            le concept 'std::weakly_incrementable<boost::spirit::istream_iterator>' a la valeur false.  
1>                                C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\__msvc_iter_core.hpp(397,18):  
1>                                le concept 'std::same_as<boost::spirit::multi_pass<std::basic_istream<char,std::char_traits<char>>,boost::spirit::iterator_policies::default_policy<boost::spirit::iterator_policies::ref_counted,boost::spirit::iterator_policies::no_check,boost::spirit::iterator_policies::istream,boost::spirit::iterator_policies::split_std_deque>>&,boost::spirit::istream_iterator&>' a la valeur false.  
1>                                    C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\__msvc_iter_core.hpp(397,18):  
1>                                    'boost::spirit::multi_pass<std::basic_istream<char,std::char_traits<char>>,boost::spirit::iterator_policies::default_policy<boost::spirit::iterator_policies::ref_counted,boost::spirit::iterator_policies::no_check,boost::spirit::iterator_policies::istream,boost::spirit::iterator_policies::split_std_deque>> &' et 'boost::spirit::istream_iterator &' sont des types différents.  
1>                                C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\__msvc_iter_core.hpp(397,7):  
1>                                l’expression a été résolue en un appel à « boost::spirit::multi_pass<std::basic_istream<char,std::char_traits<char>>,boost::spirit::iterator_policies::default_policy<boost::spirit::iterator_policies::ref_counted,boost::spirit::iterator_policies::no_check,boost::spirit::iterator_policies::istream,boost::spirit::iterator_policies::split_std_deque>>::operator ++ »  
1>                                D:\repos\in-progress\include\boost\spirit\home\support\iterators\multi_pass.hpp(110,21):  
1>                                voir la déclaration de 'boost::spirit::multi_pass<std::basic_istream<char,std::char_traits<char>>,boost::spirit::iterator_policies::default_policy<boost::spirit::iterator_policies::ref_counted,boost::spirit::iterator_policies::no_check,boost::spirit::iterator_policies::istream,boost::spirit::iterator_policies::split_std_deque>>::operator ++'  
1>    D:\repos\in-progress\include\boost\parser\parser.hpp(8872,10):  
1>    ou       'bool boost::parser::prefix_parse(I &,S,const boost::parser::parser_interface<Parser,GlobalState,ErrorHandler> &,const boost::parser::parser_interface<SkipParser,boost::parser::detail::nope,boost::parser::default_error_handler> &,Attr &,boost::parser::trace)'  
1>        D:\repos\in-progress\Program\in-progress\.trash\main.cpp(64,23):  
1>        'bool boost::parser::prefix_parse(I &,S,const boost::parser::parser_interface<Parser,GlobalState,ErrorHandler> &,const boost::parser::parser_interface<SkipParser,boost::parser::detail::nope,boost::parser::default_error_handler> &,Attr &,boost::parser::trace)' : attend des arguments 6 - 4 fournis.  
1>    D:\repos\in-progress\include\boost\parser\parser.hpp(8766,10):  
1>    ou       'auto boost::parser::prefix_parse(I &,S,const boost::parser::parser_interface<Parser,GlobalState,ErrorHandler> &,boost::parser::trace)'  
1>        D:\repos\in-progress\Program\in-progress\.trash\main.cpp(64,60):  
1>        'initialisation' : impossible de convertir de 'const boost::parser::parser_interface<boost::parser::ws_parser<false,false>,boost::parser::detail::nope,boost::parser::default_error_handler>' en 'boost::parser::trace'  
1>            D:\repos\in-progress\Program\in-progress\.trash\main.cpp(64,60):  
1>            Aucun opérateur de conversion définie par l'utilisateur disponible qui puisse effectuer cette conversion, ou l'opérateur ne peut pas être appelé  
1>    D:\repos\in-progress\include\boost\parser\parser.hpp(8634,10):  
1>    ou       'bool boost::parser::prefix_parse(I &,S,const boost::parser::parser_interface<Parser,GlobalState,ErrorHandler> &,Attr &,boost::parser::trace)'  
1>        D:\repos\in-progress\Program\in-progress\.trash\main.cpp(64,23):  
1>        les contraintes associées ne sont pas remplies  
1>            D:\repos\in-progress\include\boost\parser\parser.hpp(8614,9):  
1>            le concept 'boost::parser::parsable_iter<boost::spirit::istream_iterator>' a la valeur false.  
1>                D:\repos\in-progress\include\boost\parser\concepts.hpp(31,9):  
1>                le concept 'std::forward_iterator<boost::spirit::istream_iterator>' a la valeur false.  
1>                    C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\xutility(993,28):  
1>                    le concept 'std::input_iterator<boost::spirit::istream_iterator>' a la valeur false.  
1>                        C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\xutility(984,26):  
1>                        le concept 'std::input_or_output_iterator<boost::spirit::istream_iterator>' a la valeur false.  
1>                            C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\__msvc_iter_core.hpp(404,6):  
1>                            le concept 'std::weakly_incrementable<boost::spirit::istream_iterator>' a la valeur false.  
1>                                C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\__msvc_iter_core.hpp(397,18):  
1>                                le concept 'std::same_as<boost::spirit::multi_pass<std::basic_istream<char,std::char_traits<char>>,boost::spirit::iterator_policies::default_policy<boost::spirit::iterator_policies::ref_counted,boost::spirit::iterator_policies::no_check,boost::spirit::iterator_policies::istream,boost::spirit::iterator_policies::split_std_deque>>&,boost::spirit::istream_iterator&>' a la valeur false.  
1>                                    C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\__msvc_iter_core.hpp(397,18):  
1>                                    'boost::spirit::multi_pass<std::basic_istream<char,std::char_traits<char>>,boost::spirit::iterator_policies::default_policy<boost::spirit::iterator_policies::ref_counted,boost::spirit::iterator_policies::no_check,boost::spirit::iterator_policies::istream,boost::spirit::iterator_policies::split_std_deque>> &' et 'boost::spirit::istream_iterator &' sont des types différents.  
1>                                C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\__msvc_iter_core.hpp(397,7):  
1>                                l’expression a été résolue en un appel à « boost::spirit::multi_pass<std::basic_istream<char,std::char_traits<char>>,boost::spirit::iterator_policies::default_policy<boost::spirit::iterator_policies::ref_counted,boost::spirit::iterator_policies::no_check,boost::spirit::iterator_policies::istream,boost::spirit::iterator_policies::split_std_deque>>::operator ++ »  
1>                                D:\repos\in-progress\include\boost\spirit\home\support\iterators\multi_pass.hpp(110,21):  
1>                                voir la déclaration de 'boost::spirit::multi_pass<std::basic_istream<char,std::char_traits<char>>,boost::spirit::iterator_policies::default_policy<boost::spirit::iterator_policies::ref_counted,boost::spirit::iterator_policies::no_check,boost::spirit::iterator_policies::istream,boost::spirit::iterator_policies::split_std_deque>>::operator ++'  
```   
  
PS : The fix of `boost::spirit::istream_iterator` was proposed by Joaquim.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-11-27 17:11:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/839#issuecomment-3586826030  

This part:  
```cpp  
    istream_iterator operator++(int)  
    {  
      istream_iterator res;  
      super::operator++();  
      return res;  
    }  
```  
should be:  
```cpp  
    istream_iterator operator++(int)  
    {  
      istream_iterator res = *this;  
      super::operator++();  
      return res;  
    }  
```

---

## Comment 2

> Username: djowel  
> Created at: 2025-11-28 00:16:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/839#issuecomment-3587559844  

Thanks @joaquintides. Would you or @marc-viala-ametek care to submit a PR? That would be much appreciated!

---

## Comment 3

> Username: marc-viala-ametek  
> Created at: 2025-11-28 07:15:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/839#issuecomment-3588190609  

Humm... @djowel I'm not very familar w/ your workflow to submit a PR. By the way, the fix was carefully implemented by Joaqium, so, my first feeling, is to leave Joaquim to proceed.

---

## Comment 4

> Username: joaquintides  
> Created at: 2025-11-28 08:06:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/839#issuecomment-3588307542  

Yes, I can do that.

---

## Comment 5

> Username: djowel  
> Created at: 2025-11-29 01:13:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/839#issuecomment-3590773518  

> Yes, I can do that.  
  
Wonderful! Much appreciated!
