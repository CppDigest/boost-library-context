# #823 - Regression in Boost 1.81 for initializer lists [Closed]

> Username: saki-dev  
> Created at: 2022-12-21 14:22:43 UTC  
> Updated at: 2022-12-21 15:10:09 UTC  
> Closed at: 2022-12-21 14:48:57 UTC  
> Url: https://github.com/boostorg/json/issues/823  

Environment:  
- Visual Studio 2022 c17.4.3  
- Example code compiles with Boost 1.80, but not with 1.81  
  
Example code similar to examples in documentation with added vector:  
```  
#include <boost/json.hpp>  
#include <iostream>  
#include <string>  
#include <vector>  
  
struct customer  
{  
    int id;  
    std::string name;  
    bool current;  
    std::vector<int> dimensions;  
};  
  
void tag_invoke(boost::json::value_from_tag, boost::json::value& jv, customer const& c)  
{  
    jv =   
    {  
        { "id" , c.id },  
        { "name", c.name },  
        { "current", c.current },  
        { "dimensions", c.dimensions }  
    };  
}  
  
int main()  
{  
    customer cust{ 1, "bar", true, {20, 20, 20} };  
    auto jsonValue = boost::json::value_from(cust);  
    std::cout << boost::json::serialize(jsonValue);  
}  
```  
  
Error log:  
```  
1>CodeTests.cpp  
1>_redacted_path_\boost\boost\json\impl\value_ref.hpp(35,17): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::json::value'  
1>_redacted_path_\boost\boost\json\impl\value_ref.hpp(35,17): message : No constructor could take the source type, or constructor overload resolution was ambiguous  
1>_redacted_path_\boost\boost\json\value_ref.hpp(204,1): message : see reference to function template instantiation 'boost::json::value boost::json::value_ref::from_const<std::vector<int,std::allocator<int>>>(const void *,boost::json::storage_ptr)' being compiled  
1>_redacted_path_\CodeTests\CodeTests.cpp(24,5): message : see reference to function template instantiation 'boost::json::value_ref::value_ref<std::vector<int,std::allocator<int>>>(const T &,void *) noexcept' being compiled  
1>        with  
1>        [  
1>            T=std::vector<int,std::allocator<int>>  
1>        ]  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-12-21 14:41:53 UTC  
> Url: https://github.com/boostorg/json/issues/823#issuecomment-1361407806  

The problem is with `{ "dimensions", c.dimensions }`. This was never supposed to work. Please, replace with `{ "dimensions", value_from(c.dimensions) }`.

---

## Comment 2

> Username: saki-dev  
> Created at: 2022-12-21 14:48:57 UTC  
> Url: https://github.com/boostorg/json/issues/823#issuecomment-1361425178  

Yeah, now it works. Sorry for bothering you. I supposed it was a regression as it was working with older version of Boost.

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-12-21 14:49:58 UTC  
> Updated at: 2022-12-21 14:50:11 UTC  
> Url: https://github.com/boostorg/json/issues/823#issuecomment-1361427565  

Sure, I can understand your confusion.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-12-21 15:10:09 UTC  
> Url: https://github.com/boostorg/json/issues/823#issuecomment-1361458689  

Or replace with `BOOST_DESCRIBE_STRUCT(customer, (), (id, name, current, dimensions))` :-)
