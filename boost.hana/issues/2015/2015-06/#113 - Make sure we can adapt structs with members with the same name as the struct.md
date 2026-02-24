# #113 - [Struct] Make sure we can adapt structs with members with the same name as the struct [Closed]

> Username: ldionne  
> Created at: 2015-06-14 16:10:53 UTC  
> Updated at: 2015-06-14 16:53:36 UTC  
> Closed at: 2015-06-14 16:53:36 UTC  
> Url: https://github.com/boostorg/hana/issues/113  

The following code should compile:  
  
``` c++  
#include <boost/hana/struct.hpp>  
using namespace boost::hana;  
  
  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (int, Person)  
    );  
};  
  
struct Employee {  
    int Employee;  
};  
  
BOOST_HANA_ADAPT_STRUCT(Employee, (int, Employee));  
  
int main() { }  
```  
  
This bug popped up while fixing #112.
