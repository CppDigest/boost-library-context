# #195 - Broken interaction between std::vector and ublas::c_vector [Open]

> Username: fcooper8472  
> Created at: 2024-06-03 15:56:27 UTC  
> Updated at: 2024-06-03 15:56:27 UTC  
> Url: https://github.com/boostorg/ublas/issues/195  

Full interactive example: https://gcc.godbolt.org/z/xe4xh7Wcn  
  
Under certain conditions, when copying a 1-dimensional `c_vector`, for example when resizing or filling a `std::vector`, GCC emits a warning to do with `__builtin_memcpy`; specifically `[-Werror=array-bounds=]`.  
  
This appears to only be a problem under the following circumstances:  
  
- GCC >= 13  
- Optimizations enabled that include inlining  
- The c_vector is 1-dimensional  
  
Full code to reproduce:  
  
```cpp  
#include <boost/numeric/ublas/vector.hpp>  
  
#include <cstdlib>  
#include <vector>  
  
using boost::numeric::ublas::c_vector;  
  
template <unsigned DIM>  
int do_something()  
{  
    c_vector<double, DIM> x;  
    for(unsigned i = 0; i < DIM; ++i) {  
        x[i] = static_cast<double>(std::rand());  
    }  
  
    std::vector<c_vector<double, DIM>> vec_of_cvecs;  
    vec_of_cvecs.resize(3, x);  
  
    return vec_of_cvecs.front()[0];  
}  
  
int main() {  
  
    return do_something<1>();    // produces __builtin_memcpy warning  
    // return do_something<2>(); // works fine  
    // return do_something<2>(); // works fine  
}  
```  
  
The warning:  
  
```  
In file included from /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/algorithm:60,  
                 from /opt/compiler-explorer/libs/boost_1_83_0/boost/numeric/ublas/storage.hpp:16,  
                 from /opt/compiler-explorer/libs/boost_1_83_0/boost/numeric/ublas/vector.hpp:21,  
                 from <source>:1:  
In static member function 'static _Up* std::__copy_move<_IsMove, true, std::random_access_iterator_tag>::__copy_m(_Tp*, _Tp*, _Up*) [with _Tp = const double; _Up = double; bool _IsMove = false]',  
    inlined from '_OI std::__copy_move_a2(_II, _II, _OI) [with bool _IsMove = false; _II = const double*; _OI = double*]' at /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/stl_algobase.h:506:30,  
    inlined from '_OI std::__copy_move_a1(_II, _II, _OI) [with bool _IsMove = false; _II = const double*; _OI = double*]' at /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/stl_algobase.h:533:42,  
    inlined from '_OI std::__copy_move_a(_II, _II, _OI) [with bool _IsMove = false; _II = const double*; _OI = double*]' at /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/stl_algobase.h:540:31,  
    inlined from '_OI std::copy(_II, _II, _OI) [with _II = const double*; _OI = double*]' at /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/stl_algobase.h:633:7,  
    inlined from 'boost::numeric::ublas::c_vector<T, N>& boost::numeric::ublas::c_vector<T, N>::operator=(const boost::numeric::ublas::c_vector<T, N>&) [with T = double; long unsigned int N = 1]' at /opt/compiler-explorer/libs/boost_1_83_0/boost/numeric/ublas/vector.hpp:2556:21,  
    inlined from 'typename __gnu_cxx::__enable_if<(! std::__is_scalar<_Tp>::__value), void>::__type std::__fill_a1(_ForwardIterator, _ForwardIterator, const _Tp&) [with _ForwardIterator = boost::numeric::ublas::c_vector<double, 1>*; _Tp = boost::numeric::ublas::c_vector<double, 1>]' at /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/stl_algobase.h:919:11,  
    inlined from 'void std::__fill_a(_FIte, _FIte, const _Tp&) [with _FIte = boost::numeric::ublas::c_vector<double, 1>*; _Tp = boost::numeric::ublas::c_vector<double, 1>]' at /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/stl_algobase.h:977:21,  
    inlined from 'void std::fill(_ForwardIterator, _ForwardIterator, const _Tp&) [with _ForwardIterator = boost::numeric::ublas::c_vector<double, 1>*; _Tp = boost::numeric::ublas::c_vector<double, 1>]' at /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/stl_algobase.h:1007:20,  
    inlined from 'void std::vector<_Tp, _Alloc>::_M_fill_insert(iterator, size_type, const value_type&) [with _Tp = boost::numeric::ublas::c_vector<double, 1>; _Alloc = std::allocator<boost::numeric::ublas::c_vector<double, 1> >]' at /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/vector.tcc:556:14:  
/opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/stl_algobase.h:437:30: error: 'void* __builtin_memcpy(void*, const void*, long unsigned int)' forming offset 24 is out of the bounds [0, 24] of object '__tmp' with type 'std::vector<boost::numeric::ublas::c_vector<double, 1>, std::allocator<boost::numeric::ublas::c_vector<double, 1> > >::_Temporary_value' [-Werror=array-bounds=]  
  437 |             __builtin_memmove(__result, __first, sizeof(_Tp) * _Num);  
      |             ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/vector:72,  
                 from /opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/functional:64,  
                 from /opt/compiler-explorer/libs/boost_1_83_0/boost/numeric/ublas/functional.hpp:16,  
                 from /opt/compiler-explorer/libs/boost_1_83_0/boost/numeric/ublas/expression_types.hpp:16,  
                 from /opt/compiler-explorer/libs/boost_1_83_0/boost/numeric/ublas/vector_expression.hpp:16,  
                 from /opt/compiler-explorer/libs/boost_1_83_0/boost/numeric/ublas/vector.hpp:22:  
/opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/vector.tcc: In member function 'void std::vector<_Tp, _Alloc>::_M_fill_insert(iterator, size_type, const value_type&) [with _Tp = boost::numeric::ublas::c_vector<double, 1>; _Alloc = std::allocator<boost::numeric::ublas::c_vector<double, 1> >]':  
/opt/compiler-explorer/gcc-13.2.0/include/c++/13.2.0/bits/vector.tcc:540:32: note: '__tmp' declared here  
  540 |               _Temporary_value __tmp(this, __x);  
      |                                ^~~~~  
```  
  
Is this likely a problem with ublas? With GCC? Something it's safe for me to ignore? Thank you.
