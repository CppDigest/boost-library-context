# #59 - C++98 GCC 8 assignment does nothing [Closed]

> Username: Kojoley  
> Created at: 2018-10-01 20:39:25 UTC  
> Updated at: 2018-10-15 14:36:18 UTC  
> Closed at: 2018-10-15 14:36:18 UTC  
> Url: https://github.com/boostorg/optional/issues/59  

GCC 8.1.0/8.2.0/9.0.0 `g++ -std=c++98` fails, `g++ -std=c++11` is fine.  
The problem is not presented on GCC 7.3.0.  
  
MWE:  
```cpp  
#include <boost/detail/lightweight_test.hpp>  
#include <boost/optional.hpp>  
  
int main()  
{  
  boost::optional<int> a, b(1);  
  BOOST_TEST(!a);  
  BOOST_TEST(!!b);  
  a = b;  
  BOOST_TEST(!!a); // Fails on GCC 8 -std=c++98  
  
  return boost::report_errors();  
}  
```  
https://wandbox.org/permlink/UPpKB0gpchSAaW61

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-01 21:28:25 UTC  
> Url: https://github.com/boostorg/optional/issues/59#issuecomment-426069325  

The problem goes away if I declare a copy assignment operator for `tc_optional_base`  
  
```cpp  
    tc_optional_base& operator=( tc_optional_base const& rhs )  
    {  
        m_initialized = rhs.m_initialized;  
        m_storage = rhs.m_storage;  
        return *this;  
    }  
```

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-10-01 21:30:36 UTC  
> Url: https://github.com/boostorg/optional/issues/59#issuecomment-426069959  

Hmm, it also goes away if I make this:  
```cpp  
    void assign ( tc_optional_base const& rhs )   
    {  
      //this->operator=(rhs);  
      *this = rhs;  
    }  
```

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-10-01 22:21:53 UTC  
> Updated at: 2018-10-05 15:56:04 UTC  
> Url: https://github.com/boostorg/optional/issues/59#issuecomment-426083813  

It looks like a GCC bug. MWE (fails on any `-std`):  
```cpp  
struct dummy {} ;  
  
template <typename T>  
struct foo : dummy  
{  
    foo() {}  
    foo(T v_) : v(v_) {}  
    void assign(foo const& rhs)  
    {  
        this->operator=(rhs);     // the assignment does nothing  
        //(*this).operator=(rhs); // this one does nothing too  
        //*this = rhs;            // this one works as expected  
    }  
  
    T v;  
};  
  
template <typename T>  
struct bar : foo<T>  
{  
    typedef foo<T> base;  
    bar() : base() {}  
    bar(T v) : base(v) {}  
    bar& operator=(bar const& rhs)  
    {  
        this->assign( static_cast<base const&>(rhs) ) ;  
        return *this ;  
    }  
};  
  
int main()  
{  
    bar<int> a, b(123);  
    a.assign(b);  
    if (a.v != 123) throw "problem!";  
}  
```  
  
If you remove inheriting from `dummy` or detemplatize the classes it will also solve the problem.

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-10-05 15:40:13 UTC  
> Url: https://github.com/boostorg/optional/issues/59#issuecomment-427408835  

GCC bug report https://gcc.gnu.org/bugzilla/show_bug.cgi?id=87531
