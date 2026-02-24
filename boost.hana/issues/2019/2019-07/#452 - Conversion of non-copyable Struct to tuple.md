# #452 - Conversion of non-copyable Struct to tuple [Open]

> Username: sdebionne  
> Created at: 2019-07-09 09:57:48 UTC  
> Updated at: 2019-07-10 14:03:14 UTC  
> Url: https://github.com/boostorg/hana/issues/452  

I have the following use case that converts a non-copyable Struct to tuple:  
  
```cpp  
#include <string>  
#include <boost/hana.hpp>  
  
struct person {  
    person (person const&) = delete;  
  
    std::string name;  
    unsigned short age;  
};  
  
BOOST_HANA_ADAPT_STRUCT(person , name, age);  
  
int main()  
{  
    auto t = boost::hana::to_tuple(std::declval<person>());  
    return 0;  
}  
```  
  
that fails to compile with the following error:  
  
```  
boost/hana/detail/ebo.hpp:73:40: error: use of deleted function 'person::person(const person&)'  
             : data_(static_cast<T&&>(t))  
```  
  
Since I did not see any restriction in the documentation regarding copyability, I wonder if this is expected and/or could be worked around?
