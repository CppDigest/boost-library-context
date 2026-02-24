# #56 - boost::locale::boundary::segment_index throws bad_cast [Closed]

> Username: serbenet  
> Created at: 2020-05-17 19:02:56 UTC  
> Updated at: 2023-03-05 14:42:43 UTC  
> Closed at: 2023-03-05 14:42:43 UTC  
> Url: https://github.com/boostorg/locale/issues/56  

I am trying to execute the code below. It is a code sample from boost [documentation ](https://www.boost.org/doc/libs/1_73_0/libs/locale/doc/html/boundary_analysys.html#boundary_analysys_segments) that perfoms word extraction from a text. Unfortunately it crashes with a bad_cast thrown.  
  
```  
using namespace boost::locale::boundary;  
boost::locale::generator gen;  
std::string text = "To be or not to be, that is the question.";  
ssegment_index map(word, text.begin(), text.end(), gen("en_US.UTF-8"));  
for (ssegment_index::iterator it = map.begin(), e = map.end(); it != e; ++it)  
    std::cout << "\"" << *it << "\", ";  
std::cout << std::endl;  
```  
The exception is thrown when trying to construct segment_index, I do not understand why.  
  
Here is the call stack:  
  
```  
KernelBase.dll!00007ffc391ca839()   Unknown  
vcruntime140d.dll!00007ffc13dfb230()    Unknown  
Test.exe!std::_Throw_bad_cast() Line 66 C++  
Test.exe!std::use_facet<boost::locale::boundary::boundary_indexing<char>>(const std::locale & _Loc) Line 439    C++  
Test.exe!boost::locale::boundary::details::mapping_traits<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>,std::random_access_iterator_tag>::map(boost::locale::boundary::boundary_type t, std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>> b, std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>> e, const std::locale & l) Line 98  C++  
Test.exe!boost::locale::boundary::details::mapping<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>>::mapping<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>>(boost::locale::boundary::boundary_type type, std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>> begin, std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>> end, const std::locale & loc) Line 127    C++  
Test.exe!boost::locale::boundary::segment_index<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>>::segment_index<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>>(boost::locale::boundary::boundary_type type, std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>> begin, std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>> end, const std::locale & loc) Line 630 C++  
Test.exe!main() Line 12 C++  
```  
I am using Visual Studio 2019 with boost 1.73 installed using vcpkg (vcpkg.exe install boost-locale[icu]:x64-windows).  
  
Thanks in advance for your help.

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-03-05 10:18:59 UTC  
> Url: https://github.com/boostorg/locale/issues/56#issuecomment-1455048246  

Hi @serbenet   
  
The error originates from an access to a facet of a `std::locale` which is only available with the ICU backend. It looks like you either have a version built without ICU support or explicitly selected a different backend on runtime (`localization_backend_manager::select`)  
  
I'll include a change to improve the error  bit with #149
