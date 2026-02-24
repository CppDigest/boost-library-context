# #47 - lexical_cast<unsigned> returns unexpected result when using with split_iterator<std::wstring::iterator> [Closed]

> Username: apolukhin  
> Created at: 2021-05-02 17:33:15 UTC  
> Updated at: 2024-02-15 08:42:11 UTC  
> Closed at: 2024-02-15 08:42:11 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/47  

The following code will print "4948.4948" instead of expected "10.10" (This are ascii codes of the symbols instead of values):  
```cpp  
std::wstring wstr(L"10.10");  
typedef boost::split_iterator<std::wstring::iterator> wsplit_iter_t;  
wsplit_iter_t wdot_iter = boost::make_split_iterator( wstr, boost::first_finder(L "."));  
std::cout<<boost::lexical_cast<unsigned>(*wdot_iter++)<<'.'<<boost::lexical_cast<unsigned>(*wdot_iter++);  
```  
  
Issue on trac: https://svn.boost.org/trac10/ticket/8261

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-02-15 08:07:40 UTC  
> Updated at: 2024-02-15 08:41:33 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/47#issuecomment-1945546546  

The examples does not compile any more on C++20 and newer:  
```cpp  
#include <boost/algorithm/string/find_iterator.hpp>  
#include <boost/algorithm/string/predicate.hpp>  
#include <boost/lexical_cast.hpp>  
#include <iostream>  
  
int main() {  
    std::wstring wstr(L"10.10");  
    typedef boost::split_iterator<std::wstring::iterator> wsplit_iter_t;  
    auto wdot_iter =  
        boost::make_split_iterator(wstr, boost::first_finder(L"."));  
    std::cout << boost::lexical_cast<unsigned>(*wdot_iter++) << '.'  
              << boost::lexical_cast<unsigned>(*wdot_iter++);  
}  
```  
  
This is dues to https://wg21.link/p1423 that deleted the problematic operators in the C++ Standard Library
