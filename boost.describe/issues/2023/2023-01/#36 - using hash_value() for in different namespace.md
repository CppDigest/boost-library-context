# #36 - using hash_value() for in different namespace [Closed]

> Username: ghost  
> Created at: 2023-01-31 15:05:22 UTC  
> Updated at: 2023-01-31 15:43:49 UTC  
> Closed at: 2023-01-31 15:43:49 UTC  
> Url: https://github.com/boostorg/describe/issues/36  

Hi.  
I'm trying to implement a hash_value() from this [example](https://github.com/boostorg/describe/blob/develop/example/hash_value.cpp#L23), but I'm getting a compilation error  
  
[full gist](https://gist.github.com/yevka/702ee8a3bae3125934ac2ce1caaf686a)  
  
```cpp  
#include <iostream>  
  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
#include <boost/container_hash/hash.hpp>  
  
#include <gtest/gtest.h>  
  
  
#if BOOST_VERSION < 108100  
  
template <class T, class Bd = boost::describe::describe_bases<T, boost::describe::mod_any_access>,  
          class Md = boost::describe::describe_members<T, boost::describe::mod_any_access>>  
std::size_t hash_value(const T& t) {  
    std::size_t r = 0;  
    boost::mp11::mp_for_each<Bd>([&](auto D) {  
        using B = typename decltype(D)::type;  
        boost::hash_combine(r, (const B&)t);  
    });  
    boost::mp11::mp_for_each<Md>([&](auto D) { boost::hash_combine(r, t.*D.pointer); });  
    return r;  
}  
  
#endif  
  
  
namespace namespace_a {  
struct PODTypeN1 {  
    int a;  
    int b;  
    int c;  
};  
BOOST_DESCRIBE_STRUCT(PODTypeN1, (), (a, b, c))  
} // namespace namespace_a  
  
namespace namespace_b {  
struct PODTypeN2 {  
    float d;  
    double e;  
    bool f;  
};  
BOOST_DESCRIBE_STRUCT(PODTypeN2, (), (d, e, f))  
} // namespace namespace_b  
  
namespace namespace_c {  
struct ComplexPodTypeN {  
    namespace_a::PODTypeN1 podType1;  
    namespace_b::PODTypeN2 podType2;  
};  
BOOST_DESCRIBE_STRUCT(ComplexPodTypeN, (), (podType1, podType2))  
} // namespace namespace_c  
  
namespace test {  
  
TEST(test_hash, ComplexPodType_with_namespace) {  
    namespace_c::ComplexPodTypeN a = {  
        {1, 2, 3},  
        {0.3, 0.4, true}  
    };  
    namespace_c::ComplexPodTypeN b = a;  
  
    auto hash_a = boost::hash<namespace_c::ComplexPodTypeN>()(a);  
    auto hash_b = boost::hash<namespace_c::ComplexPodTypeN>()(b);  
    std::cout << "hash_a = " << hash_a << std::endl;  
    std::cout << "hash_b = " << hash_b << std::endl;  
    ASSERT_TRUE((hash_a == hash_b));  
  
    b.podType2.e = 42.42;  
    hash_b = boost::hash<namespace_c::ComplexPodTypeN>()(b);  
    std::cout << "hash_a = " << hash_a << std::endl;  
    std::cout << "hash_b = " << hash_b << std::endl;  
    ASSERT_TRUE((hash_a != hash_b));  
}  
  
} // namespace test  
  
```  
  
```  
Scanning dependencies of target boost_describe  
[ 50%] Building CXX object CMakeFiles/boost_describe.dir/test_hash.cpp.o  
In file included from /home/pftest/.conan/data/boost/1.78.0/_/_/package/4c0d21de97108c6489e888fd2e50a95c38b9f3aa/include/boost/container_hash/hash.hpp:766:0,  
                 from /home/pftest/Documents/boost_describe3/test_hash.cpp:5:  
/home/pftest/.conan/data/boost/1.78.0/_/_/package/4c0d21de97108c6489e888fd2e50a95c38b9f3aa/include/boost/container_hash/extensions.hpp: In instantiation of ‘std::size_t boost::hash<T>::operator()(const T&) const [with T = namespace_c::ComplexPodTypeN; std::size_t = long unsigned int]’:  
/home/pftest/Documents/boost_describe3/test_hash.cpp:62:64:   required from here  
/home/pftest/.conan/data/boost/1.78.0/_/_/package/4c0d21de97108c6489e888fd2e50a95c38b9f3aa/include/boost/container_hash/extensions.hpp:305:30: error: no matching function for call to ‘hash_value(const namespace_c::ComplexPodTypeN&)’  
             return hash_value(val);  
  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-01-31 15:36:08 UTC  
> Updated at: 2023-01-31 15:37:02 UTC  
> Url: https://github.com/boostorg/describe/issues/36#issuecomment-1410601721  

This will work as-is under Boost 1.81, but if you need to support earlier versions, you have to define the overloads of `hash_value` in the corresponding namespace of the type. The easiest way to do it is to add a using declaration for your existing `hash_value`:  
```  
#if BOOST_VERSION < 108100  
using ::hash_value;  
#endif  
```  
after each `BOOST_DESCRIBE_STRUCT`, as in https://godbolt.org/z/M1n3Me7eY.  
  
You can also put the common definition of `hash_value` into a namespace of your own, it doesn't need to be global.  
  
(Unrelated, unless you're on C++20, you can also add `using boost::describe::operator==` and `using boost::describe::operator!=` to generate the equality operators, as these are generally required to use the hash containers.)

---

## Comment 2

> Username: ghost  
> Created at: 2023-01-31 15:43:41 UTC  
> Url: https://github.com/boostorg/describe/issues/36#issuecomment-1410618749  

ths
