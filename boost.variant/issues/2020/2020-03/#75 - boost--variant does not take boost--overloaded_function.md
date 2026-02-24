# #75 - boost::variant does not take boost::overloaded_function [Closed]

> Username: AlexGuteniev  
> Created at: 2020-03-20 06:48:51 UTC  
> Updated at: 2020-12-19 11:12:26 UTC  
> Closed at: 2020-12-19 11:12:26 UTC  
> Url: https://github.com/boostorg/variant/issues/75  

`boost::variant::apply_visitor` relies on `result_type` typedef.   
  
This works:  
```  
    boost::variant<const char*, int> v("hello");  
  
    struct  
    {  
        using result_type = int;  
  
        int operator()(int v) const  
        {  
            std::cout << v << '\n';  
            return 0;  
        }  
  
        int operator()(const char* v) const  
        {  
            std::cout << v << '\n';  
            return 0;  
        }  
  
    } struct_visitor;  
  
  
    v.apply_visitor(struct_visitor);  
```  
  
This does not:  
```  
    auto overloaded_function_visitor = boost::make_overloaded_function(  
        [](const char* v)  
        {  
            std::cout << v << '\n';  
            return 0;  
        },  
  
        [](int v)  
        {  
            std::cout << v << '\n';  
            return 0;  
        }  
        );  
  
    v.apply_visitor(overloaded_function_visitor);  
```  
  
Apparently, this can be changed from `boost::variant` side, since `boost::overloaded_function` already has result type:  
  
https://github.com/boostorg/functional/blob/7120c2f840fafe72787221c104c1a173c91ff270/include/boost/functional/overloaded_function.hpp#L175-L177

---

## Comment 1

> Username: AlexGuteniev  
> Created at: 2020-03-20 07:14:10 UTC  
> Url: https://github.com/boostorg/variant/issues/75#issuecomment-601563876  

`boost::apply_visitor(overloaded_function_visitor, v);` works, but I'm not sure why `v.apply_visitor(overloaded_function_visitor);` shouldn't

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-12-19 11:12:26 UTC  
> Url: https://github.com/boostorg/variant/issues/75#issuecomment-748459434  

`boost::variant::apply_visitor` is not a part of [public API](https://www.boost.org/doc/libs/1_75_0/doc/html/boost/variant.html).  
  
Just don't use it :)
