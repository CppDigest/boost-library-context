# #75 - Test test3_mvov failed when running in clang and clang-cl on Windows [Open]

> Username: yuxianch  
> Created at: 2019-11-19 08:48:04 UTC  
> Updated at: 2019-12-01 12:28:00 UTC  
> Url: https://github.com/boostorg/ublas/issues/75  

Hi,  
I found that [Test test3_mvov](https://github.com/boostorg/ublas/blob/master/IDEs/qtcreator/test/test3_mvov.pro) failed for map/set iterators incompatible in clang and clang-cl on Windows.  
Below are the commands that I used.  
```  
clang test3.cpp -o test3.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_UBLAS_NO_EXCEPTIONS -DUSE_DOUBLE -DUSE_FLOAT -DUSE_MAPPED_VECTOR_OF_MAPPED_VECTOR -DUSE_STD_COMPLEX -DUSE_STD_MAP -I"..\..\..\.."  
clang test31.cpp -o test31.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_UBLAS_NO_EXCEPTIONS -DUSE_DOUBLE -DUSE_FLOAT -DUSE_MAPPED_VECTOR_OF_MAPPED_VECTOR -DUSE_STD_COMPLEX -DUSE_STD_MAP -I"..\..\..\.."  
clang test32.cpp -o test32.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_UBLAS_NO_EXCEPTIONS -DUSE_DOUBLE -DUSE_FLOAT -DUSE_MAPPED_VECTOR_OF_MAPPED_VECTOR -DUSE_STD_COMPLEX -DUSE_STD_MAP -I"..\..\..\.."  
clang test33.cpp -o test33.obj -std=c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_UBLAS_NO_EXCEPTIONS -DUSE_DOUBLE -DUSE_FLOAT -DUSE_MAPPED_VECTOR_OF_MAPPED_VECTOR -DUSE_STD_COMPLEX -DUSE_STD_MAP -I"..\..\..\.."  
clang -o test3 test3.obj test31.obj test32.obj test33.obj  
./test3  
```  
I got lots of warnings when compiling `test32.cpp` and `test33.cpp`, which look like this.   
```  
In file included from test33.cpp:13:  
In file included from ./test3.hpp:18:  
In file included from ..\..\..\..\boost/numeric/ublas/vector.hpp:21:  
In file included from ..\..\..\..\boost/numeric/ublas/storage.hpp:26:  
In file included from ..\..\..\..\boost/numeric/ublas/traits.hpp:21:  
..\..\..\..\boost/numeric/ublas/detail/iterator.hpp:149:21: warning: 'iterator<boost::numeric::ublas::sparse_bidirectional_iterator_tag, std::complex<double>, long long, std::complex<double> *, std::complex<double> &>' is deprecated: warning STL4015: The std::iterator  
      class template (used as a base class to provide typedefs) is deprecated in C++17. (The <iterator> header is NOT deprecated.) The C++ Standard has never required user-defined iterators to derive from std::iterator. To fix this warning, stop deriving from  
      std::iterator and start providing publicly accessible typedefs named iterator_category, value_type, difference_type, pointer, and reference. Note that value_type is required to be non-const, even for constant iterators. You can define  
      _SILENCE_CXX17_ITERATOR_BASE_CLASS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning. [-Wdeprecated-declarations]  
        public std::iterator<IC, T> {  
                    ^  
```  
And I also got the following output and a window which showed me the debug assertion failing message when running the exeutable file `test3`.  
```  
test_vector  
test_matrix_vector  
float mapped_vector_of_mapped_vector  
row (m, 0) = [3](1,2,3)  
```  
As the warnings tell us, should the source code be modified to fix this issue?  
Thanks!

---

## Comment 1

> Username: bassoy  
> Created at: 2019-12-01 12:28:00 UTC  
> Url: https://github.com/boostorg/ublas/issues/75#issuecomment-560101724  

@yuxianch thanks for commenting on this issue. we might soon integrate a new iterator concept which aligns with newer C++ standards. for now we might want to live with that warning. please feel free to create a pull request for that issue.
