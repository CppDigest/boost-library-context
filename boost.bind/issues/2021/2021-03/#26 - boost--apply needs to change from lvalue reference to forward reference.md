# #26 - boost::apply needs to change from lvalue reference to forward reference [Closed]

> Username: jhcarl0814  
> Created at: 2021-03-05 07:19:20 UTC  
> Updated at: 2021-03-06 21:09:17 UTC  
> Closed at: 2021-03-06 21:09:16 UTC  
> Url: https://github.com/boostorg/bind/issues/26  

boost::apply does not compile when injecting rvalue at function/arguments positions using nested boost::bind:    
```c++  
#include<iostream>  
#include<functional>  
#include<boost/bind/bind.hpp>  
#include<boost/bind/apply.hpp>  
  
class f_t  
{  
public:  
    void operator()(int&)&  
    {  
        std::cout << 1 << std::endl;  
    }  
    void operator()(int&&)&  
    {  
        std::cout << 2 << std::endl;  
    }  
    void operator()(int&)&&  
    {  
        std::cout << 3 << std::endl;  
    }  
    void operator()(int&&)&&  
    {  
        std::cout << 4 << std::endl;  
    }  
}f;  
  
int& get_lvalue_arg()  
{  
    static int a;  
    return a;  
}  
  
int get_prvalue_arg()  
{  
    return 1;  
}  
  
auto& get_lvalue_f()  
{  
    return f;  
}  
  
auto get_prvalue_f() // function returning [(prvalue) function object]; the same goes for function returning [(prvalue) function pointer]  
{  
    return f;  
}  
  
int main()  
{  
    boost::bind(boost::apply<void>(), boost::bind(get_lvalue_f), boost::bind(get_lvalue_arg))();  
    boost::bind(boost::apply<void>(), boost::bind(get_lvalue_f), boost::bind(get_prvalue_arg))(); //compile error  
    boost::bind(boost::apply<void>(), boost::bind(get_prvalue_f), boost::bind(get_lvalue_arg))(); //compile error  
    boost::bind(boost::apply<void>(), boost::bind(get_prvalue_f), boost::bind(get_prvalue_arg))(); //compile error  
  
    boost::bind(boost::apply<void>(), boost::bind(boost::apply<f_t&>(), boost::placeholders::_1), boost::bind(boost::apply<int&>(), boost::placeholders::_2))(get_lvalue_f, get_lvalue_arg);  
    boost::bind(boost::apply<void>(), boost::bind(boost::apply<f_t&>(), boost::placeholders::_1), boost::bind(boost::apply<int>(), boost::placeholders::_2))(get_lvalue_f, get_prvalue_arg); //compile error  
    boost::bind(boost::apply<void>(), boost::bind(boost::apply<f_t>(), boost::placeholders::_1), boost::bind(boost::apply<int&>(), boost::placeholders::_2))(get_prvalue_f, get_lvalue_arg); //compile error  
    boost::bind(boost::apply<void>(), boost::bind(boost::apply<f_t>(), boost::placeholders::_1), boost::bind(boost::apply<int>(), boost::placeholders::_2))(get_prvalue_f, get_prvalue_arg); //compile error  
  
    std::bind(f, std::bind(get_lvalue_arg))();  
    std::bind(f, std::bind(get_prvalue_arg))();  
}  
```  
  
expected output:    
```  
1  
2  
3  
4  
1  
2  
3  
4  
1  
2  
```  
  
workaround:    
in <boost/bind/apply.hpp>, change:    
```c++  
template<class F, class A1> result_type operator()(F & f, A1 & a1) const  
{  
    return f(a1);  
}  
```  
to:    
```c++  
template<class F, class A1> result_type operator()(F&& f, A1&& a1) const  
{  
    return ((F&&)f)((A1&&)a1);  
}  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-06 21:09:16 UTC  
> Url: https://github.com/boostorg/bind/issues/26#issuecomment-792054942  

Should be fixed on develop. Thank you for the report.
