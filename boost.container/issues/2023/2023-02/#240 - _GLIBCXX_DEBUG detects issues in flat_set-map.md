# #240 - _GLIBCXX_DEBUG detects issues in flat_set/map [Closed]

> Username: jcelerier  
> Created at: 2023-02-23 05:01:44 UTC  
> Updated at: 2023-02-23 21:16:28 UTC  
> Closed at: 2023-02-23 21:16:28 UTC  
> Url: https://github.com/boostorg/container/issues/240  

In boost 1.81, g++ 12.2  
  
Repro:  
  
  
```c++  
#include <boost/container/flat_set.hpp>  
#include <string>  
#include <vector>  
  
boost::container::flat_set<std::string, std::less<>, std::vector<std::string>>  
    widgetFuncMap{{"floatslider"}, {"logfloatslider"}, {"intslider"},  
                  {"intspinbox"},  {"toggle"},         {"lineedit"},  
                  {"combobox"},    {"button"},         {"hsvslider"},  
                  {"xyslider"},    {"multislider"}};  
  
int main() {}  
```  
  
```  
$ g++  -D_GLIBCXX_DEBUG bug.cpp  
$ ./a.out  
/usr/include/c++/12.2.1/debug/safe_iterator.h:318:  
In function:  
    gnu_debug::_Safe_iterator<_Iterator, _Sequence, _Category>::pointer   
    gnu_debug::_Safe_iterator<_Iterator, _Sequence, _Category>::operator->()   
    const [with _Iterator = gnu_cxx::normal_iterator<std::  
    cxx11::basic_string<char>*, std::vector<std::cxx11::basic_string<char>,   
    std::allocator<std::cxx11::basic_string<char> > > >; _Sequence = std::  
    debug::vector<std::cxx11::basic_string<char> >; _Category =   
    std::forward_iterator_tag; pointer = std::cxx11::basic_string<char>*]  
  
Error: attempt to dereference a past-the-end iterator.  
  
Objects involved in the operation:  
    iterator "this" @ 0x7ffc1a98e5e0 {  
      type = gnu_cxx::normal_iterator<std::cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*, std::vector<std::cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > (mutable iterator);  
      state = past-the-end;  
      references sequence with type 'std::debug::vector<std::cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >' @ 0x55704a2f5180  
    }  
[1]    23018 IOT instruction (core dumped)  ./a.out  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2023-02-23 21:13:34 UTC  
> Url: https://github.com/boostorg/container/issues/240#issuecomment-1442440328  

Many thanks for the report. iterator_to_raw_pointer interferes with checked iterators, this is a issue that libstdc++ also has solved:  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93960
