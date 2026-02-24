# #530 - Structured bindings do not work with `environment::key_value_pair[_view]` [Open]

> Username: vovams  
> Created at: 2025-12-09 00:50:23 UTC  
> Updated at: 2025-12-09 00:50:23 UTC  
> Url: https://github.com/boostorg/process/issues/530  

Structured bindings do not work with `environment::key_value_pair` and `environment::key_value_pair_view`. For example, the following code does not compile when using GCC 15.2.1 or Clang 21.1.6 on Linux:  
  
```c++  
#include <iostream>  
#include <boost/process/environment.hpp>  
  
int main() {  
    for (const auto [name, value] : boost::process::environment::current())  
        std::cout << name << '=' << value << '\n';  
    return 0;  
}  
```  
  
<details>  
<summary>Compiler output</summary>  
  
```  
$ g++ bug.cpp -lboost_process  
bug.cpp: In function ‘int main()’:  
bug.cpp:5:21: error: cannot decompose inaccessible member ‘boost::process::v2::environment::key_value_pair_view::value_’ of ‘const boost::process::v2::environment::key_value_pair_view’  
    5 |     for (const auto [name, value] : boost::process::environment::current())  
      |                     ^~~~~~~~~~~~~  
In file included from /usr/include/boost/process/environment.hpp:1,  
                 from bug.cpp:2:  
/usr/include/boost/process/v2/environment.hpp:435:20: note: declared private here  
  435 |   string_view_type value_;  
      |                    ^~~~~~  
$ clang bug.cpp -lboost_process  
bug.cpp:5:21: error: type 'const key_value_pair_view' decomposes into 1 element, but 2 names were  
      provided  
    5 |     for (const auto [name, value] : boost::process::environment::current())  
      |                     ^  
/usr/include/boost/process/v2/environment.hpp:1377:14: note: selected 'begin' function with  
      iterator type 'iterator'  
 1377 |     iterator begin() const {return iterator(handle_.get());}  
      |              ^  
1 error generated.  
```  
  
</details>  
  
Interestingly, if `const auto` is replaced with just `auto`, GCC compiles the code without any warnings, while Clang still does not compile it but produces a different error message, that seems to explain the actual cause:  
  
```  
bug.cpp:5:15: error: 'value' is a private member of 'std::integral_constant<unsigned long, 2>'  
    5 |     for (auto [name, value] : boost::process::environment::current())  
      |               ^  
/usr/include/boost/process/v2/environment.hpp:1280:82: note: constrained by implicitly private  
      inheritance here  
 1280 | class tuple_size<BOOST_PROCESS_V2_NAMESPACE::environment::key_value_pair_view> : integral_constant<std::size_t, 2u> {};  
      |                                                                                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/bin/../lib64/gcc/x86_64-suse-linux/15/../../../../include/c++/15/type_traits:94:28: note:   
      member is declared here  
   94 |       static constexpr _Tp value = __v;  
      |                            ^  
1 error generated.  
```
