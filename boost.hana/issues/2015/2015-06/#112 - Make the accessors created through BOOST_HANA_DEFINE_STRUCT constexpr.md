# #112 - [Struct] Make the accessors created through BOOST_HANA_DEFINE_STRUCT constexpr. [Closed]

> Username: ldionne  
> Created at: 2015-06-14 15:25:01 UTC  
> Updated at: 2015-06-14 16:53:45 UTC  
> Closed at: 2015-06-14 16:53:45 UTC  
> Url: https://github.com/boostorg/hana/issues/112  

The following code should compile:  
  
``` c++  
#include <boost/hana/struct.hpp>  
using namespace boost::hana;  
  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (int, age)  
    );  
};  
  
constexpr auto acc = accessors<Person>();  
  
int main() { }  
```
