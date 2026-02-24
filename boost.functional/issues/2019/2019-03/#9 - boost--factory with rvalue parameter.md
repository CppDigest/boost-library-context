# #9 - boost::factory with rvalue parameter [Closed]

> Username: RegSerg  
> Created at: 2019-03-13 05:55:39 UTC  
> Updated at: 2019-08-28 18:00:32 UTC  
> Closed at: 2019-08-28 18:00:32 UTC  
> Url: https://github.com/boostorg/functional/issues/9  

### Configurations:  
boost 1.69 + Visual Studio 2017  
### Problem:  
the code does not compile when the boost :: factory object is called with the rvalue parameters. This behavior is documented in the description. Quote from [boost factory](https://www.boost.org/doc/libs/1_69_0/libs/functional/factory/doc/html/index.html#boost_functional_factory.brief_description)  
> For technical reasons the arguments to the function objects have to be LValues. A factory that also accepts RValues can be composed using the boost::forward_adapter or boost::bind.   
  
It would be very desirable to remove this restriction.  
 Code snippet with problem code below:  
```  
#include <iostream>  
#include <string>  
#include <boost/functional/factory.hpp>  
  
struct AAA  
{  
    AAA(int i) :i_(i)  
    {}  
    void f(const std::string & text)  
    {  
        std::cout << "AAA::f(\"" << text << "\"),  i_ =  " << i_ << std::endl;  
    }  
    int i_;  
};  
int main()  
{  
    auto a = boost::factory<AAA*>();  
    const int i = 2;  
    a(i)->f("a(i)");         //work  
    a(i+1)->f("a(i+1)");  //does not compile  
    a(4)->f("a(4)");        //does not compile  
//  error C2664: 'AAA *boost::factory<AAA *,void,boost::factory_alloc_for_pointee_and_deleter>::operator ()(void) const': cannot convert argument 1 from 'int' to 'T0 &'  
}  
```

---

## Comment 1

> Username: sknepnek  
> Created at: 2019-06-12 12:59:43 UTC  
> Updated at: 2019-06-14 22:18:01 UTC  
> Url: https://github.com/boostorg/functional/issues/9#issuecomment-501261774  

This code compiles on Linux with gcc 8.3.1 with -std=c++98 if   
  
auto a = boost::factory<AAA*>();  
  
is replaced with  
  
boost::function<AAA*(int)> a = boost::factory<AAA*>();   
  
It compiles with the C++ 11 standard if one changes the constructor to  
AAA(const int& i) : i_(i)  
    {}  
  
with   
  
boost::function<AAA*(const int&)> a = boost::factory<AAA*>();   
  
the version with   
auto a = boost::factory<AAA*>();   
  
does not compile with C++11 even with the modified constructor.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-07-11 11:46:03 UTC  
> Url: https://github.com/boostorg/functional/issues/9#issuecomment-510452133  

+1; this is being reported as a problem against `boost::function` in https://github.com/boostorg/function/issues/36.

---

## Comment 3

> Username: glenfe  
> Created at: 2019-08-27 11:11:06 UTC  
> Url: https://github.com/boostorg/functional/issues/9#issuecomment-525254574  

This is addressed with 4403017952664934eb45ad4b7acb11e2532da181. It will be merged to master within a week, and will release with Boost 1.72.
