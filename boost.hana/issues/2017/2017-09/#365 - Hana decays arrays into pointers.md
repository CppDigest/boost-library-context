# #365 - Hana decays arrays into pointers [Closed]

> Username: anthonygclark  
> Created at: 2017-09-03 13:30:52 UTC  
> Updated at: 2018-01-01 22:38:40 UTC  
> Closed at: 2018-01-01 22:38:40 UTC  
> Url: https://github.com/boostorg/hana/issues/365  

It appears boost::hana decays arrays into pointers. Boost::fusion doesn't seem to do this, at least when using BOOST_FUSION_ADAPT_STRUCT. See attached code:  
  
```cpp  
#include <boost/core/demangle.hpp>  
#include <boost/hana/adapt_struct.hpp>  
#include <boost/hana/for_each.hpp>  
#include <boost/hana/members.hpp>  
  
#include <iostream>  
  
struct Foo  
{  
    float array[3];  
    float scalar;  
};  
  
BOOST_HANA_ADAPT_STRUCT(  
    Foo,  
    array,  
    scalar  
);  
  
int main()  
{  
    Foo f;  
    f.array[0] = 1.1;  
    f.array[1] = 2.2;  
    f.array[2] = 3.3;  
    f.scalar = 4.4;  
      
    auto const members = boost::hana::members(f);  
    std::cout << boost::core::demangle(typeid(members[boost::hana::size_c<0>]).name()) << '\n';  
    std::cout << boost::core::demangle(typeid(members[boost::hana::size_c<1>]).name()) << '\n';  
}  
  
/* output:  
 * $ ./a.out  
 * float *  
 * float  
 */  
```

---

## Comment 1

> Username: anthonygclark  
> Created at: 2017-09-05 18:38:24 UTC  
> Url: https://github.com/boostorg/hana/issues/365#issuecomment-327265238  

Just a small follow up on this. If arrays decay into pointers, then it's near impossible to iterate them as if they were a bounded array. My use case was to print via std::ostream the value of my class's members, but I can't do this when arrays decay into pointers since I can't reasonably know the bounds of the array. I know I should be using std::array or std::vector, but I don't want to change code that uses fixed sized arrays if I don't have to.

---

## Comment 2

> Username: ldionne  
> Created at: 2018-01-01 21:20:36 UTC  
> Url: https://github.com/boostorg/hana/issues/365#issuecomment-354677978  

Sorry for the late reply. Your problem stems from the fact that `hana::members(some_struct)` returns a Hana `Sequence` containing the members of the struct (concretely it's a `hana::tuple`), and the arrays are decayed into pointers when this sequence is constructed.

---

## Comment 3

> Username: ldionne  
> Created at: 2018-01-01 22:07:42 UTC  
> Url: https://github.com/boostorg/hana/issues/365#issuecomment-354680660  

I tried fixing the problem and then realized that `std::tuple` can also not be constructed using arrays when it contains arrays: https://wandbox.org/permlink/UWRsfbjB21dhyhcG . This is per the standard, which says that the variadic constructor should be enabled when `std::is_constructible<T, Arg>` is true (in our case `std::is_constructible<int[3], int (&)[3]>`), and that is not satisfied.  
  
So I won't be making `hana::tuple` support that extension, since it does complicate things quite a bit. However, I do have a solution for you that does not involve switching over to `std::array` (which would be better, though). You can use `hana::accessors` to manually retrieve the members of your struct without any decaying:  
  
```c++  
#include <boost/core/demangle.hpp>  
#include <boost/hana.hpp>  
  
#include <iostream>  
  
struct Foo  
{  
    float array[3];  
    float scalar;  
};  
  
BOOST_HANA_ADAPT_STRUCT(  
    Foo,  
    array,  
    scalar  
);  
  
int main()  
{  
    Foo f;  
    f.array[0] = 1.1;  
    f.array[1] = 2.2;  
    f.array[2] = 3.3;  
    f.scalar = 4.4;  
      
    auto const getters = boost::hana::transform(boost::hana::accessors<Foo>(), boost::hana::second);  
    std::cout << boost::core::demangle(typeid(getters[boost::hana::size_c<0>](f)).name()) << '\n';  
    std::cout << boost::core::demangle(typeid(getters[boost::hana::size_c<1>](f)).name()) << '\n';  
}  
  
/* output:  
 * $ ./a.out  
 * float [3]  
 * float  
 */  
```  
  
[live example](https://wandbox.org/permlink/xFgMO7M9yst11I38)
