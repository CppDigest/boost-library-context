# #82 - Error when compiling tensor with NDEBUG [Open]

> Username: dubek  
> Created at: 2020-05-04 19:23:03 UTC  
> Updated at: 2022-08-31 07:29:04 UTC  
> Url: https://github.com/boostorg/ublas/issues/82  

I'm compiling a simple program which includes the `tensor.hpp` header:  
  
```c++  
#include <boost/numeric/ublas/tensor.hpp>  
#include <ostream>  
  
int main()  
{  
    auto A = boost::numeric::ublas::tensor<float>{3,4,2};  
    std::cout << "A=" << A << std::endl;  
    return 0;  
}  
```  
  
This works fine if I don't define `NDEBUG`. However, in release mode with (`-DNDEBUG` argument to the compiler), this fails:  
  
```  
In file included from /celibs/boost_1_72_0/boost/numeric/ublas/tensor.hpp:22,  
                 from <source>:1:  
/celibs/boost_1_72_0/boost/numeric/ublas/tensor/strides.hpp:215:9: error: expected unqualified-id before '<' token  
  215 | template<class size_type, class layout_type>  
      |         ^  
/celibs/boost_1_72_0/boost/numeric/ublas/tensor/strides.hpp:223:2: error: expected ';' before 'inline'  
  223 | }  
      |  ^  
      |  ;  
In file included from /celibs/boost_1_72_0/boost/numeric/ublas/tensor.hpp:24,  
                 from <source>:1:  
/celibs/boost_1_72_0/boost/numeric/ublas/tensor/tensor.hpp:333:2: error: expected unqualified-id before 'template'  
  333 |  template<class other_layout>  
      |  ^~~~~~~~  
/celibs/boost_1_72_0/boost/numeric/ublas/tensor/tensor.hpp:355:2: error: expected unqualified-id before 'template'  
  355 |  template<class derived_type>  
      |  ^~~~~~~~  
  
...  
```  
  
As you can see in [compiler explorer](https://godbolt.org/z/RFCnQs), this happens both on gcc 9.3 and clang 10.0.0 with these command-line options: `-DNDEBUG -std=c++17`  
  
I also found that adding `#define BOOST_UBLAS_INLINE` before including `tensor.hpp` solves the compilation problem (but, of course, cancels inlining which probably hurts performance).  
  
Am I using the wrong compilation options? ublas' [documentation](https://github.com/boostorg/ublas/blob/develop/doc/index.html#L402) says I should define `NDEBUG` preprocessor symbol to get proper performance (release mode). I assume tensor was tested with `-DNDEBUG` and `-O3` etc. BTW I think that the error reported in #79 might also be related to `-DNDEBUG`.

---

## Comment 1

> Username: bassoy  
> Created at: 2020-05-05 06:19:27 UTC  
> Url: https://github.com/boostorg/ublas/issues/82#issuecomment-623875642  

> As you can see in compiler explorer, this happens both on gcc 9.3 and clang 10.0.0 with these command-line options: -DNDEBUG -std=c++17  
  
Thanks @dubek for reporting this issue. Your observation is correct and this is a bug. Sorry for the inconvenience. I doubt that you will have severe performance penalties for tensor operations when not using the `NDEBUG` option. We have not yet optimized tensor operations such as done in this [repo](https://github.com/bassoy/ttv).  
  
In general, Boost.uBlas is in a transition and we are about to generalize the tensor concept. The `BOOST_UBLAS_INLINE` pragma will be depecrated for `tensor` after Boost.uBlas version 1.73.

---

## Comment 2

> Username: dubek  
> Created at: 2020-05-05 06:44:33 UTC  
> Url: https://github.com/boostorg/ublas/issues/82#issuecomment-623882743  

Thanks, I'll look at TTV. The problem with compiling without `NDEBUG` is that I lose optimizations for other parts of Boost that I use in my program.

---

## Comment 3

> Username: bassoy  
> Created at: 2020-05-05 07:30:10 UTC  
> Url: https://github.com/boostorg/ublas/issues/82#issuecomment-623899250  

> Thanks, I'll look at TTV. The problem with compiling without `NDEBUG` is that I lose optimizations for other parts of Boost that I use in my program.  
  
Sorry to hear that. We do our best to yield a high-code coverage with unit-tests.   
Could you tell me just roughly what applications you use for tensor operations. Are their any missing tensor operations or functions?

---

## Comment 4

> Username: yjwen  
> Created at: 2021-12-06 07:55:55 UTC  
> Updated at: 2021-12-06 07:56:37 UTC  
> Url: https://github.com/boostorg/ublas/issues/82#issuecomment-986522094  

Met the same compilation error with NDEBUG defined today. After some digging into tensor's code, I guess it is due to the wrong position of `BOOST_UBLAS_INLINE` placed before `template` keyword in some tensor/tensor.hpp and tensor/strides.hpp.  
  
Take the code at line 214-216 of strides.hpp for example.  
```  
BOOST_UBLAS_INLINE  
template<class size_type, class layout_type>  
auto access(std::vector<size_type> const& i, basic_strides<size_type,layout_type> const& w)  
```  
When NDEBUG is defined, `BOOST_UBLAS_INLINE` is defined as `inline` in ublas/detail/config.hpp. The above code is expanded to  
```  
inline  
template<class size_type, class layout_type>  
auto access(std::vector<size_type> const& i, basic_strides<size_type,layout_type> const& w)  
```  
which cause syntax error.  
  
The proper code should be `inline` following `template`, that requires BOOST_UBLAS_INLINE being placed after `template`  
  
There are several such BOOST_UBLAS_INLINE before `template` should be moved to after `template`.  
  
The issue is still seen with boost 1.77.0 release.  
  
Regards,  
Yujie

---

## Comment 5

> Username: bassoy  
> Created at: 2022-02-09 09:23:48 UTC  
> Url: https://github.com/boostorg/ublas/issues/82#issuecomment-1033538284  

This is changed with commit https://github.com/boostorg/ublas/commit/231ba5f7303f0f7d7ae5d5f9426e4908fb4df712 in branch [develop](https://github.com/boostorg/ublas).   
  
Yet not merged into the master branch which is why you still see the issue in boost version 1.77.0.

---

## Comment 6

> Username: huang-lihao  
> Created at: 2022-08-31 07:29:04 UTC  
> Url: https://github.com/boostorg/ublas/issues/82#issuecomment-1232566427  

This problem still exits in v1.80.0
