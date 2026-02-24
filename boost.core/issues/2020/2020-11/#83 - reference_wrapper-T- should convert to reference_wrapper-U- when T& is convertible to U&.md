# #83 - reference_wrapper<T> should convert to reference_wrapper<U> when T& is convertible to U& [Closed]

> Username: apolukhin  
> Created at: 2020-11-18 19:36:50 UTC  
> Updated at: 2020-12-19 17:11:45 UTC  
> Closed at: 2020-12-19 17:11:44 UTC  
> Url: https://github.com/boostorg/core/issues/83  

This is an issue from trac https://svn.boost.org/trac10/ticket/1112  
  
The following code shows a case that works well with `std::reference_wrapper`, but fails with `boost;:reference_wrapper`  
```cpp  
#include <iostream>  
#include <boost/variant.hpp>  
  
class base {  
public :  
     virtual void print() const {  
         std::cout << "base\n";  
     }  
};  
class child : public base {  
public :  
     void print() const {  
         std::cout << "child\n";  
     }  
};  
  
class my_visitor{  
public:  
     void operator()(base& b) const  
     {  
         b.print();  
     }  
  
     void operator()(int i) const  
     {  
         std::cout << "int\n";  
     }  
};  
  
#include <boost/ref.hpp>  
  
int main()  
{  
     child c;  
  
#if 1 // CHANGE ME  
    using std::ref;  
    using std::reference_wrapper;  
#else  
    using boost::ref;  
    using boost::reference_wrapper;  
#endif  
     typedef boost::variant<reference_wrapper<base>, int> var_t;  
     var_t v = ref(c);  
  
     boost::apply_visitor( my_visitor(), v ); //prints "child"  
  
     base b;  
     v = ref(b);  
     boost::apply_visitor( my_visitor(), v ); //prints "base"  
  
     std::cin.get();  
     return 0;  
}   
```  
  
Godbolt playground: https://godbolt.org/z/bTWrz3

---

## Comment 1

> Username: pdimov  
> Created at: 2020-11-20 02:44:12 UTC  
> Url: https://github.com/boostorg/core/issues/83#issuecomment-730809606  

Should be fixed on develop.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-12-19 17:11:44 UTC  
> Url: https://github.com/boostorg/core/issues/83#issuecomment-748499749  

Merged to master.
