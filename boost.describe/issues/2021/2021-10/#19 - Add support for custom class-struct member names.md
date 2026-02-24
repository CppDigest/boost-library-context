# #19 - Add support for custom class/struct member names [Open]

> Username: phprus  
> Created at: 2021-10-05 16:53:56 UTC  
> Updated at: 2021-10-05 16:53:56 UTC  
> Url: https://github.com/boostorg/describe/issues/19  

I suggest adding support for custom class/struct member names to the library.  
Like this example:  
  
```c++  
#include <boost/describe.hpp>  
#include <iostream>  
  
struct my_struct  
{  
    int   a;  
    float b;  
    char  c;  
};  
  
BOOST_DESCRIBE_STRUCT(  
    my_struct, (), (  
        (a, "a_name"),  
        (b, "b_name"),  
        (c)  
    )  
)  
  
int main()  
{  
    boost::mp11::mp_for_each<  
        boost::describe::describe_members<my_struct,  
        boost::describe::mod_any_access>  
    >([&](auto D){  
        std::cout << D.name << std::endl;  
    });   
  
    // Output:  
    //    a_name  
    //    b_name  
    //    c  
  
    return 0;  
}  
```
