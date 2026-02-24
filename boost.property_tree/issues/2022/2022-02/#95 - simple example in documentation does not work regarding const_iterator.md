# #95 - simple example in documentation does not work regarding const_iterator [Closed]

> Username: TomFD  
> Created at: 2022-02-10 19:44:27 UTC  
> Updated at: 2023-11-17 14:38:24 UTC  
> Closed at: 2023-11-17 14:38:24 UTC  
> Url: https://github.com/boostorg/property_tree/issues/95  

Hi,  
  
the simple example found here, https://www.boost.org/doc/libs/1_78_0/doc/html/property_tree/accessing.html  
gives the error below on the full code:  
```  
#include <boost/property_tree/xml_parser.hpp>  
#include <iostream>  
  
using boost::property_tree::ptree;  
  
int main() {  
    ptree pt;  
    pt.push_back(ptree::value_type("pi", ptree("3.14159")));  
    ptree::const_iterator it = pt.find("pi");  
}  
```  
  
C:\Users\tsch\source\repos\ConsoleApplication2\ConsoleApplication2.cpp(10,45): error C2440: "Initialisierung": "boost::property_tree::basic_ptree<std::string,std::string,std::less<Key>>::assoc_iterator" kann nicht in "boost::property_tree::basic_ptree<std::string,std::string,std::less<Key>>::const_iterator" konvertiert werden  
1>        with  
1>        [  
1>            Key=std::string  
1>        ]  
  
of course, it works with 'assoc_iterator', but if this is intended, the documentation needs a change.  
  
Thomas

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-02-10 19:45:40 UTC  
> Url: https://github.com/boostorg/property_tree/issues/95#issuecomment-1035413654  

Thank you for highlighting.
