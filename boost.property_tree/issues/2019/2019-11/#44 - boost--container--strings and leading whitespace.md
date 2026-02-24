# #44 - boost::container::strings and leading whitespace [Closed]

> Username: alanbarr  
> Created at: 2019-11-04 21:22:50 UTC  
> Updated at: 2023-11-15 11:25:11 UTC  
> Closed at: 2023-11-15 11:25:11 UTC  
> Url: https://github.com/boostorg/property_tree/issues/44  

Hi,  
  
I've noticed some strange behaviour when reading out values into a `boost::container::string`.  
Where a value begins with leading spaces it is not present when retrieved with `get()`.  
This behaves the same on `1.68.0` and `1.71.0`.  
  
For the program below, I expect the output to be the same for both `std::string` and `boost::container::string`.  However `std::string` correctly preserves the leading space  (`"       
  value"`) where as `boost:container::string` does not (`"value"`).  
  
#  Output  
```  
### boost::container::string ###  
{  
    "key": "       value"  
}  
  
"value"  
  
### std::string ###  
{  
    "key": "       value"  
}  
  
"       value"  
  
```  
  
# Program  
  
```  
#include <boost/container/string.hpp>  
#include <boost/property_tree/json_parser.hpp>  
#include <boost/property_tree/ptree.hpp>  
#include <iostream>  
  
#define LEADING_WS "       value"  
  
void print_ptree(boost::property_tree::ptree const & pt)  
{  
    std::stringstream stream;  
    boost::property_tree::write_json(stream, pt);  
    std::cout << stream.str() << std::endl;  
}  
  
void boost_container_string(void)  
{  
    boost::container::string const value(LEADING_WS);  
  
    boost::property_tree::ptree pt;  
    pt.put<boost::container::string>("key", value);  
  
    boost::container::string read_back = pt.get<boost::container::string>("key");  
  
    std::cout << "### boost::container::string ###" << std::endl;  
    print_ptree(pt);  
    std::cout << "\"" << read_back << "\"" << std::endl;  
    std::cout << std::endl;  
}  
  
void std_string(void)  
{  
    std::string const value(LEADING_WS);  
  
    boost::property_tree::ptree pt;  
    pt.put<std::string>("key", value);  
  
    std::string read_back = pt.get<std::string>("key");  
  
    std::cout << "### std::string ###" << std::endl;  
    print_ptree(pt);  
    std::cout << "\"" << read_back << "\"" << std::endl;  
    std::cout << std::endl;  
}  
  
int main()  
{  
    boost_container_string();  
    std_string();  
  
    return 0;  
}  
```

---

## Comment 1

> Username: alanbarr  
> Created at: 2019-11-05 20:15:20 UTC  
> Url: https://github.com/boostorg/property_tree/issues/44#issuecomment-550000379  

Additionally if a string has white space in it an exception is / can be raised.  
E.g. change the now poorly named macro in the above program to:  
`#define LEADING_WS "va lue"`  
  
An exception is raised:  
```  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::property_tree::ptree_bad_data> >'  
  what():  conversion of data to type "N5boost9container12basic_stringIcSt11char_traitsIcENS0_13new_allocatorIcEEEE" failed  
```  
  
# GDB  
```  
(gdb) where  
#0  __GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:51  
#1  0x00007ffff70e5801 in __GI_abort () at abort.c:79  
#2  0x00007ffff7ad8957 in ?? () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#3  0x00007ffff7adeab6 in ?? () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#4  0x00007ffff7adeaf1 in std::terminate() () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#5  0x00007ffff7aded24 in __cxa_throw () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#6  0x0000000000405dd9 in boost::throw_exception<boost::exception_detail::error_info_injector<boost::property_tree::ptree_bad_data> > (e=...)  
    at /usr/include/boost/throw_exception.hpp:69  
#7  0x000000000040592f in boost::exception_detail::throw_exception_<boost::property_tree::ptree_bad_data> (x=...,   
    current_function=0x417d49 "typename boost::enable_if<detail::is_translator<Translator>, Type>::type boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char>, std::less<std::__cxx11::b"..., file=0x4170bd "/usr/include/boost/property_tree/detail/ptree_implementation.hpp", line=672)  
    at /usr/include/boost/throw_exception.hpp:86  
#8  0x00000000004139d3 in boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::get_value<boost::container::basic_string<char, std::char_traits<char>, boost::container::new_allocator<char> >, boost::property_tree::stream_translator<char, std::char_traits<char>, std::allocator<char>, boost::container::basic_string<char, std::char_traits<char>, boost::container::new_allocator<char> > > > (this=0x6360a0, tr=...)  
    at /usr/include/boost/property_tree/detail/ptree_implementation.hpp:670  
#9  0x0000000000413646 in boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::get_value<boost::container::basic_string<char, std::char_traits<char>, boost::container::new_allocator<char> > > (this=0x6360a0)  
    at /usr/include/boost/property_tree/detail/ptree_implementation.hpp:679  
#10 0x00000000004041fe in boost::property_tree::basic_ptree<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::get<boost::container::basic_string<char, std::char_traits<char>, boost::container::new_allocator<char> > > (this=0x7fffffffd650, path=...)  
    at /usr/include/boost/property_tree/detail/ptree_implementation.hpp:751  
#11 0x0000000000403870 in boost_container_string () at main.cpp:22  
#12 0x0000000000403c44 in main () at main.cpp:47  
  
```  
  
# Compilers  
  
- `clang version 9.0.0`  
- `g++ (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0`

---

## Comment 2

> Username: ashtum  
> Created at: 2023-11-15 11:25:11 UTC  
> Url: https://github.com/boostorg/property_tree/issues/44#issuecomment-1812340489  

The distinction lies in the `get` function. When using `pt.get<std::string>("key")`, it employs [id_translator](https://github.com/boostorg/property_tree/blob/develop/include/boost/property_tree/id_translator.hpp#L24), which directly returns the internal `std::string` as is. However, with `pt.get<boost::container::string>("key")`, it utilizes [stream_translator](https://github.com/boostorg/property_tree/blob/develop/include/boost/property_tree/stream_translator.hpp), leveraging the overloaded stream extraction operator to retrieve the value. Consequently, ignores leading and trailing spaces.  
  
If you use `boost::property_tree::basic_ptree<std::string, boost::container::string>` as the ptree type, the results will be as expected.
