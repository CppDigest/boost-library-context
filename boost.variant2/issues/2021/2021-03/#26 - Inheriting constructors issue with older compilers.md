# #26 - Inheriting constructors issue with older compilers [Closed]

> Username: sdebionne  
> Created at: 2021-03-12 15:54:44 UTC  
> Updated at: 2021-03-15 08:32:50 UTC  
> Closed at: 2021-03-13 16:26:35 UTC  
> Url: https://github.com/boostorg/variant2/issues/26  

With clang < 4 and gcc < 7 and probably latest MSVC, the following code and [godbolt](https://godbolt.org/z/fs44cz) fail to compile:  
  
```c++  
#include <boost/variant2/variant.hpp>  
  
template <typename ...T>  
class any_type : boost::variant2::variant<T...>  
{  
    using parent_t = boost::variant2::variant<T...>;  
  
    using parent_t::parent_t;  
};  
  
struct foo  
{  
    foo() {}  
    foo(int i_arg) : i(i_arg) {}  
    foo(foo const& rhs) {}  
  
    template <typename T>  
    foo(T const& rhs) {  
        rhs.bar();  
    }  
  
    int i{0};  
};  
  
int main(int argc, char *argv[])  
{  
    using any_t = any_type<int, double, foo>;  
  
    any_t a(1);  
    any_t b(a);  
}  
```  
  
The issue comes from `foo`'s converting constructor.  
  
Related to boostorg/gil#526

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-12 19:40:39 UTC  
> Url: https://github.com/boostorg/variant2/issues/26#issuecomment-797710879  

Interesting, I hadn't noticed that the derivation was even private. Yes, this fails with MSVC (and it fails with their own `std::variant` too.) I'm not quite sure what makes the newer GCC and Clang compile it.  
  
I can make this work, but I need to think a bit more about it, make sure there's nothing legitimate that the fix would break.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-03-13 16:26:35 UTC  
> Url: https://github.com/boostorg/variant2/issues/26#issuecomment-798588489  

Merged the fix to develop.

---

## Comment 3

> Username: sdebionne  
> Created at: 2021-03-15 08:32:50 UTC  
> Url: https://github.com/boostorg/variant2/issues/26#issuecomment-799224324  

Awesome thanks! Since you mention MSVC's `std::variant`, I took the initiative to report the issue there as well.
