# #193 - Compile failed with field typed `std::optional<Eigen::Matrix3d>` [Open]

> Username: CHN-beta  
> Created at: 2024-11-27 19:47:37 UTC  
> Updated at: 2024-12-22 15:43:03 UTC  
> Url: https://github.com/boostorg/pfr/issues/193  

The following code is not compiled:  
```c++  
# include <Eigen/Dense>  
# include <boost/pfr.hpp>  
# include <boost/pfr/core_name.hpp>  
  
...  
  
  struct B  
  {  
    std::optional<Eigen::Matrix3d> a;  
  } f;  
  
  boost::pfr::for_each_field(f, [&](const auto& field, auto index)  
  {  
    using type = std::remove_cvref_t<decltype(field)>;  
    std::cout << nameof::nameof_full_type<type>() << std::endl;  
    auto name = boost::pfr::get_name<decltype(index)::value, B>();  
    std::cout << name << std::endl;  
  });  
```  
  
With the following error:  
```  
[build] /nix/store/38ibz461pxzsi5jshlhf10qylzfc4vkn-clang-wrapper-18.1.8/bin/clang++ -DBOOST_ALL_NO_LIB -DBOOST_ATOMIC_DYN_LINK -DBOOST_ATOMIC_NO_LIB -DBOOST_FILESYSTEM_DYN_LINK -DBOOST_FILESYSTEM_NO_LIB -DBOOST_IOSTREAMS_DYN_LINK -DBOOST_IOSTREAMS_NO_LIB -DBOOST_SYSTEM_DYN_LINK -DBOOST_SYSTEM_NO_LIB -DFMT_SHARED -DH5_USE_BOOST -DH5_USE_EIGEN -DHIGHFIVE_HAS_CONCEPTS=1 -DMPI_NO_CPPBIND -I/home/chn/repo/nixos/packages/biu/include -isystem /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3 -isystem /nix/store/21fky8f0114kmd1dq3svwsgram9c5f3v-concurrencpp/include/concurrencpp-0.1.7 -g -std=gnu++23 -Wno-gnu-string-literal-operator-template -MD -MT CMakeFiles/test-yaml.dir/test/yaml.cpp.o -MF CMakeFiles/test-yaml.dir/test/yaml.cpp.o.d -o CMakeFiles/test-yaml.dir/test/yaml.cpp.o -c /home/chn/repo/nixos/packages/biu/test/yaml.cpp  
[build] In file included from /home/chn/repo/nixos/packages/biu/test/yaml.cpp:1:  
[build] In file included from /home/chn/repo/nixos/packages/biu/include/biu.hpp:8:  
[build] In file included from /home/chn/repo/nixos/packages/biu/include/biu/eigen.tpp:3:  
[build] In file included from /home/chn/repo/nixos/packages/biu/include/biu/eigen.hpp:4:  
[build] In file included from /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/Dense:1:  
[build] In file included from /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/Core:295:  
[build] /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/src/Core/Matrix.h:332:22: error: call to member function '_init1' is ambiguous  
[build]   332 |       Base::template _init1<T>(x);  
[build]       |       ~~~~~~~~~~~~~~~^~~~~~~~~  
[build] /nix/store/605hgp0v6rf9945vmvz2pizz069h5bvc-gcc-13.3.0/include/c++/13.3.0/optional:214:8: note: in instantiation of function template specialization 'Eigen::Matrix<double, 3, 3>::Matrix<boost::pfr::detail::ubiq_lref_constructor>' requested here  
[build]   214 |             : _M_value(std::forward<_Args>(__args)...)  
[build]       |               ^  
[build] /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/src/Core/PlainObjectBase.h:839:30: note: candidate function [with T = boost::pfr::detail::ubiq_lref_constructor]  
[build]   839 |     EIGEN_STRONG_INLINE void _init1(Index size, typename internal::enable_if<    (Base::SizeAtCompileTime!=1 || !internal::is_convertible<T, Scalar>::value)  
[build]       |                              ^  
[build] /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/src/Core/PlainObjectBase.h:875:30: note: candidate function [with T = boost::pfr::detail::ubiq_lref_constructor]  
[build]   875 |     EIGEN_STRONG_INLINE void _init1(const Scalar* data){  
[build]       |                              ^  
[build] /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/src/Core/PlainObjectBase.h:889:30: note: candidate function [with T = boost::pfr::detail::ubiq_lref_constructor]  
[build]   889 |     EIGEN_STRONG_INLINE void _init1(const Derived& other){  
[build]       |                              ^  
[build] In file included from /home/chn/repo/nixos/packages/biu/test/yaml.cpp:1:  
[build] In file included from /home/chn/repo/nixos/packages/biu/include/biu.hpp:8:  
[build] In file included from /home/chn/repo/nixos/packages/biu/include/biu/eigen.tpp:3:  
[build] In file included from /home/chn/repo/nixos/packages/biu/include/biu/eigen.hpp:4:  
[build] In file included from /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/Dense:1:  
[build] In file included from /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/Core:295:  
[build] /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/src/Core/Matrix.h:332:22: error: call to member function '_init1' is ambiguous  
[build]   332 |       Base::template _init1<T>(x);  
[build]       |       ~~~~~~~~~~~~~~~^~~~~~~~~  
[build] /nix/store/605hgp0v6rf9945vmvz2pizz069h5bvc-gcc-13.3.0/include/c++/13.3.0/optional:214:8: note: in instantiation of function template specialization 'Eigen::Matrix<double, 3, 3>::Matrix<boost::pfr::detail::ubiq_lref_base_asserting<B>>' requested here  
[build]   214 |             : _M_value(std::forward<_Args>(__args)...)  
[build]       |               ^  
[build] /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/src/Core/PlainObjectBase.h:839:30: note: candidate function [with T = boost::pfr::detail::ubiq_lref_base_asserting<B>]  
[build]   839 |     EIGEN_STRONG_INLINE void _init1(Index size, typename internal::enable_if<    (Base::SizeAtCompileTime!=1 || !internal::is_convertible<T, Scalar>::value)  
[build]       |                              ^  
[build] /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/src/Core/PlainObjectBase.h:875:30: note: candidate function [with T = boost::pfr::detail::ubiq_lref_base_asserting<B>]  
[build]   875 |     EIGEN_STRONG_INLINE void _init1(const Scalar* data){  
[build]       |                              ^  
[build] /nix/store/192041k004z7is2v7c29mgjq715m6w74-eigen-3.4.0/include/eigen3/Eigen/src/Core/PlainObjectBase.h:889:30: note: candidate function [with T = boost::pfr::detail::ubiq_lref_base_asserting<B>]  
[build]   889 |     EIGEN_STRONG_INLINE void _init1(const Derived& other){  
[build]       |                              ^  
[build] 2 errors generated.  
```  
  
Tested with C++ 23 standard, with both clang 18 and clang 19.  
  
`Eigen::Matrix3d` means a matrix containing `double` elements with size of 3x3. Eigen is a popular math library.  
  
It seems `pfr` trying to construct `std::optional<Eigen::matrix3d>` using `boost::pfr::detail::ubiq_lref_constructor`, then `optional` forwarded the construction to `Eigen::Matrix3d`.

---

## Comment 1

> Username: CHN-beta  
> Created at: 2024-12-02 02:26:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/193#issuecomment-2510430757  

I workaround it by using `std::unique_ptr<Eigen::Matrix3d>`.
