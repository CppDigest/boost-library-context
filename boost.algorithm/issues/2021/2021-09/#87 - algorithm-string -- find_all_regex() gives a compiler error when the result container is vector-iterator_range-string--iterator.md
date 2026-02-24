# #87 - algorithm/string -- find_all_regex() gives a compiler error when the result container is vector<iterator_range<string::iterator>> [Open]

> Username: Oshanath  
> Created at: 2021-09-27 12:43:09 UTC  
> Updated at: 2021-09-27 12:46:00 UTC  
> Url: https://github.com/boostorg/algorithm/issues/87  

[The doc](https://www.boost.org/doc/libs/1_77_0/doc/html/boost/algorithm/find_all_regex.html) says that the container we should pass to this function can be std::vector<boost::iterator_range<std::string::iterator>>. But when I run the program it gives a compile error. But when the result container passed, is a std::vector<std::string>, everything works fine.  
  
First code snippet is using std::vector<boost::iterator_range<std::string::iterator>> as the result.  
```  
#include "boost/algorithm/string/split.hpp"  
#include "boost/range/iterator_range_core.hpp"  
#include <iostream>  
#include <boost/algorithm/string.hpp>  
#include <boost/algorithm/string/regex.hpp>  
#include <vector>  
  
int main(int, char**) {  
      
    std::string s("Hello world");  
    boost::regex regex("\\w");  
  
    std::vector<boost::iterator_range<std::string::iterator>> result;  
    boost::find_all_regex(result, s, regex);  
  
}  
```  
  
Following is the last 3 lines of the compiler error. The whole error is too long.  
  
c:\mingw\include\c++\9.2.0\bits\stl_iterator.h:787:11: note:   no known conversion for argument 1 from '__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >' to 'const __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >&'  
  
c:\mingw\include\c++\9.2.0\bits\stl_iterator.h:787:11: note: candidate: 'constexpr __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >::__normal_iterator(__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >&&)'  
  
c:\mingw\include\c++\9.2.0\bits\stl_iterator.h:787:11: note:   no known conversion for argument 1 from '__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >' to '__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >&&'  
  
Also I ran the same program in Clang compiler too. Still the same result.  
  
But when I run the following program with the container as std::vector<std::string> everything works without a hitch.  
  
```  
#include "boost/algorithm/string/split.hpp"  
#include "boost/range/iterator_range_core.hpp"  
#include <iostream>  
#include <boost/algorithm/string.hpp>  
#include <boost/algorithm/string/regex.hpp>  
#include <string>  
#include <vector>  
  
int main(int, char**) {  
      
    std::string s("Hello world");  
    boost::regex regex("\\w");  
  
    std::vector<std::string> result;  
    boost::find_all_regex(result, s, regex);  
  
}  
```
