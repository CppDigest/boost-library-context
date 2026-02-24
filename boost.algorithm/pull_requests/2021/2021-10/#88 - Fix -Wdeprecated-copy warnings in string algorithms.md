# #88 Fix -Wdeprecated-copy warnings in string algorithms [Merged]

> Username: apolukhin  
> Created at: 2021-10-03 14:22:42 UTC  
> Updated at: 2021-12-25 10:03:08 UTC  
> Merged at: 2021-12-23 19:36:00 UTC  
> Closed at: 2021-12-23 19:36:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/88  

Compiling algorithm/string/test with command ` ../../../../b2 cxxstd=2a "cxxflags=-Werror=deprecated-copy" toolset=gcc-9 -j4` without the patch produces warnings like the following:  
```  
split_test.cpp:193:14: error: implicitly-declared ‘boost::algorithm::find_iterator<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> > >& boost::algorithm::find_iterator<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> > >::operator=(const boost::algorithm::find_iterator<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> > >&)’ is deprecated [-Werror=deprecated-copy]  
  193 |     fiter2 = fiter;  
      |              ^~~~~  
```

---

## Comment 1

> Username: mclow  
> Created_at: 2021-10-10 15:14:04 UTC  
> Url: https://github.com/boostorg/algorithm/pull/88#issuecomment-939499794  

When I run `b2 cxxstd=2a "cxxflags=-Werror=deprecated"` using clang, I get similar errors on both `find_iterator` and `split_iterator`  
  
```  
In file included from split_test.cpp:12:  
In file included from ../../../../boost/algorithm/string/split.hpp:16:  
In file included from ../../../../boost/algorithm/string/iter_find.hpp:27:  
../../../../boost/algorithm/string/find_iterator.hpp:243:13: error: definition of implicit copy assignment operator for 'split_iterator<std::__1::__wrap_iter<char *> >' is deprecated because it has a user-declared copy constructor [-Werror,-Wdeprecated]  
            split_iterator( const split_iterator& Other ) :  
            ^  
split_test.cpp:209:12: note: in implicit copy assignment operator for 'boost::algorithm::split_iterator<std::__1::__wrap_iter<char *> >' first required here  
    siter2 = siter;  
           ^  
```

---

## Comment 2

> Username: apolukhin  
> Created_at: 2021-12-11 10:27:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/88#issuecomment-991590154  

`"cxxflags=-Werror=deprecated"` gives way more warnings that I was planning to fix in this PR.  
  
Tested the patch on clang via`./b2 libs/algorithm/string/test/ cxxstd=2a,03,11,17,14 "cxxflags=-Werror=deprecated-copy" toolset=clang-13 -j4 -a`. Works fine

---

## Comment 3

> Username: mclow  
> Created_at: 2021-12-23 19:35:54 UTC  
> Url: https://github.com/boostorg/algorithm/pull/88#issuecomment-1000491906  

LGTM. Thanks!

---
