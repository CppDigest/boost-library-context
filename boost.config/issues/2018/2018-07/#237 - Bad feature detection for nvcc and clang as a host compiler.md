# #237 - Bad feature detection for nvcc and clang as a host compiler. [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 19:00:32 UTC  
> Updated at: 2018-08-04 18:21:37 UTC  
> Closed at: 2018-08-04 18:21:37 UTC  
> Url: https://github.com/boostorg/config/issues/237  

Boost feature detection is wrong if the source is compiled with nvcc and clang as host compiler.  
  
For example, move semantics can't be used with c++11 enabled.  
```  
#include <utility>                                                                                                                                                                                              
  
#include <boost/variant.hpp>                                                                                                                                                                                    
  
class TestClass                                                                                                                                                                                                 
{                                                                                                                                                                                                               
public:                                                                                                                                                                                                         
    TestClass() = default;                                                                                                                                                                                      
    TestClass(const TestClass &) = delete;                                                                                                                                                                      
    TestClass(TestClass &&) = default;                                                                                                                                                                          
};                                                                                                                                                                                                              
  
  
int main() {                                                                                                                                                                                                    
    TestClass c1;                                                                                                                                                                                               
    boost::variant<TestClass> v{std::move(c1)};                                                                                                                                                                 
    return 0;                                                                                                                                                                                                   
}          
```  
When compiled with (ubuntu 16.04, gcc 5.4, CUDA 9.1):  
```  
nvcc temp.cu -o temp -ccbin=/usr/bin/gcc -std=c++11  
```  
everything works well, but  
```  
nvcc temp.cu -o temp -ccbin=/usr/bin/clang-4.0 -std=c++11  
```  
fails with the following:  
```  
/usr/include/boost/variant/detail/initializer.hpp(110): error: function "TestClass::TestClass(const TestClass &)"  
te`  
mp.cu(10): here cannot be referenced -- it is a deleted function  
          detected during:  
            instantiation of "int boost::detail::variant::make_initializer_node::apply<BaseIndexPair, Iterator>::initializer_node::initialize(void *, boost::detail::variant::make_initializer_node::apply<BaseIndexPair, Iterator>::initializer_node::param_T) [with BaseIndexPair=boost::mpl::pair<boost::detail::variant::initializer_root, mpl_::int_<0>>, Iterator=boost::mpl::l_iter<boost::mpl::list1<TestClass>>]"   
/usr/include/boost/variant/variant.hpp(1538): here  
            instantiation of "void boost::variant<T0_, T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T11, T12, T13, T14, T15, T16, T17, T18, T19>::convert_construct(T &, int, mpl_::false_) [with T0_=TestClass, T1=boost::detail::variant::void_, T2=boost::detail::variant::void_, T3=boost::detail::variant::void_, T4=boost::detail::variant::void_, T5=boost::detail::variant::void_, T6=boost::detail::variant::void_, T7=boost::detail::variant::void_, T8=boost::detail::variant::void_, T9=boost::detail::variant::void_, T10=boost::detail::variant::void_, T11=boost::detail::variant::void_, T12=boost::detail::variant::void_, T13=boost::detail::variant::void_, T14=boost::detail::variant::void_, T15=boost::detail::variant::void_, T16=boost::detail::variant::void_, T17=boost::detail::variant::void_, T18=boost::detail::variant::void_, T19=boost::detail::variant::void_, T=const TestClass]"   
/usr/include/boost/variant/variant.hpp(1683): here  
            instantiation of "boost::variant<T0_, T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T11, T12, T13, T14, T15, T16, T17, T18, T19>::variant(const T &) [with T0_=TestClass, T1=boost::detail::variant::void_, T2=boost::detail::variant::void_, T3=boost::detail::variant::void_, T4=boost::detail::variant::void_, T5=boost::detail::variant::void_, T6=boost::detail::variant::void_, T7=boost::detail::variant::void_, T8=boost::detail::variant::void_, T9=boost::detail::variant::void_, T10=boost::detail::variant::void_, T11=boost::detail::variant::void_, T12=boost::detail::variant::void_, T13=boost::detail::variant::void_, T14=boost::detail::variant::void_, T15=boost::detail::variant::void_, T16=boost::detail::variant::void_, T17=boost::detail::variant::void_, T18=boost::detail::variant::void_, T19=boost::detail::variant::void_, T=TestClass]"   
temp.cu(17): here  
  
1 error detected in the compilation of "/tmp/tmpxft_00005819_00000000-8_temp.cpp1.ii".  
```  
If I change boost/config/select_compiler_config.hpp  
```  
42 #elif defined __clang__ && !defined(__CUDACC__)  
```  
to  
```  
42 #elif defined __clang__  
```  
everything works well.  
  
Moved from Trac.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-04 17:55:07 UTC  
> Url: https://github.com/boostorg/config/issues/237#issuecomment-410466653  

I think I will make this change, but there is a catch here:  
  
* As things stand, clang is configured as gcc, but since it only claims to be gcc-4.2 a rather limited number of features get enabled.  
* If we configure as clang, then we might possibly enable features which are not supported by the nvcc front end - unfortunately nvcc doesn't just pre-process the .cu files to split out host and device code, it actually passes then through it's compiler (or something like a compiler).  As a result if we use a language feature that's unknown to the latest gcc version supported by nvcc, then it's also unknown to the nvcc front end and results in an error.  
  
Fortunately, latest releases of nvcc seem to be reasonable feature complete, so hopefully this won't result in too many issues.  I predict ructions come C++20 though :(

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-04 18:01:41 UTC  
> Url: https://github.com/boostorg/config/issues/237#issuecomment-410467066  

.... Normally the ultimate fix for complex setups is to use the configure script to generate a custom config, would need to use the `-x cu` option to nvcc so that the .cpp files are treated as .cu files.
