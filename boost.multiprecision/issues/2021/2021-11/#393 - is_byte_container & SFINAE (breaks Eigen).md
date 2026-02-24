# #393 - is_byte_container & SFINAE (breaks Eigen) [Closed]

> Username: mglisse  
> Created at: 2021-11-20 09:41:38 UTC  
> Updated at: 2021-12-05 09:09:17 UTC  
> Closed at: 2021-12-05 09:09:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/393  

Using the current `master` branch of Eigen and `develop` for Boost.Multiprecision, I try to compile the simple  
```c++  
#include <Eigen/Dense>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/eigen.hpp>  
  
typedef boost::multiprecision::cpp_rational NT;  
typedef Eigen::Matrix<NT,Eigen::Dynamic,Eigen::Dynamic> M;  
void f(M&m1, M const&m2){  
  m1=m2*m2;  
}  
```  
This fails with  
<pre>  
In file included from /home/glisse/repos/multiprecision/include/boost/multiprecision/cpp_int.hpp:20,  
                 from bug.cpp:2:  
/home/glisse/repos/multiprecision/include/boost/multiprecision/traits/is_byte_container.hpp: In instantiation of ‘struct boost::multiprecision::detail::is_byte_container_imp<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, true>’:  
/home/glisse/repos/multiprecision/include/boost/multiprecision/traits/is_byte_container.hpp:38:8:   required from ‘struct boost::multiprecision::detail::is_byte_container<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1> >’  
/home/glisse/repos/multiprecision/include/boost/multiprecision/cpp_int.hpp:2117:29:   required by substitution of ‘template<class Container> constexpr boost::multiprecision::backends::cpp_int_backend<>::cpp_int_backend(const Container&, const typename std::enable_if<boost::multiprecision::detail::is_byte_container<Arithmetic>::value, void>::type*) [with Container = Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>]’  
/usr/include/c++/11/type_traits:946:30:   required from ‘struct std::__is_constructible_impl<boost::multiprecision::backends::cpp_int_backend<>, Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<0, 0, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, std::allocator<long long unsigned int> > >, boost::multiprecision::et_on>, -1, -1, 0, -1, -1> >’  
/usr/include/c++/11/type_traits:952:12:   required from ‘struct std::is_constructible<boost::multiprecision::backends::cpp_int_backend<>, Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<0, 0, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, std::allocator<long long unsigned int> > >, boost::multiprecision::et_on>, -1, -1, 0, -1, -1> >’  
/home/glisse/repos/multiprecision/include/boost/multiprecision/rational_adaptor.hpp:87:112:   required by substitution of ‘template<class Arithmetic> boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> >::rational_adaptor(const Arithmetic&, const typename std::enable_if<(std::is_constructible<boost::multiprecision::backends::cpp_int_backend<>, Arithmetic>::value && (! std::is_floating_point<_Tp>::value)), void>::type*) [with Arithmetic = Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>]’  
/usr/include/c++/11/type_traits:1442:45:   required by substitution of ‘template<class _From1, class _To1, class> static std::true_type std::__is_convertible_helper<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> >, false>::__test<_From1, _To1, <template-parameter-1-3> >(int) [with _From1 = Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>; _To1 = boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> >; <template-parameter-1-3> = <missing>]’  
/usr/include/c++/11/type_traits:1451:42:   required from ‘struct std::__is_convertible_helper<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> >, false>’  
/usr/include/c++/11/type_traits:1457:12:   required from ‘struct std::is_convertible<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >’  
/home/glisse/repos/multiprecision/include/boost/multiprecision/number.hpp:50:295:   required by substitution of ‘template<class V> constexpr boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >::number(const V&, typename std::enable_if<(((((boost::multiprecision::detail::is_arithmetic<T>::value || std::is_same<std::__cxx11::basic_string<char>, V>::value) || std::is_convertible<Val, const char*>::value) && (! std::is_convertible<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >::type, boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >::value)) && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >::type, boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >::value)) && (! std::is_same<V, __float128>::value)), void>::type*) [with V = Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>]’  
/usr/include/c++/11/type_traits:1442:45:   required by substitution of ‘template<class _From1, class _To1, class> static std::true_type std::__is_convertible_helper<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, false>::__test<_From1, _To1, <template-parameter-1-3> >(int) [with _From1 = Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>; _To1 = boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >; <template-parameter-1-3> = <missing>]’  
/usr/include/c++/11/type_traits:1451:42:   required from ‘struct std::__is_convertible_helper<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, false>’  
/usr/include/c++/11/type_traits:1457:12:   required from ‘struct std::is_convertible<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > > >’  
/home/glisse/repos/eigen/Eigen/src/Core/util/XprHelper.h:98:8:   required from ‘struct Eigen::internal::promote_scalar_arg_unsupported<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, double, false, true>’  
/home/glisse/repos/eigen/Eigen/src/Core/util/XprHelper.h:86:8:   required from ‘struct Eigen::internal::promote_scalar_arg<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, false>’  
/home/glisse/repos/eigen/Eigen/src/Core/../plugins/CommonCwiseBinaryOps.h:50:1:   required by substitution of ‘template<class T> const Eigen::CwiseBinaryOp<Eigen::internal::scalar_product_op<typename Eigen::internal::promote_scalar_arg<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, T, Eigen::internal::has_ReturnType<Eigen::ScalarBinaryOpTraits<T, boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, Eigen::internal::scalar_product_op<T, boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > > > > >::value>::type, boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > > >, const typename Eigen::internal::plain_constant_type<Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>, typename Eigen::internal::promote_scalar_arg<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, T, Eigen::internal::has_ReturnType<Eigen::ScalarBinaryOpTraits<T, boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, Eigen::internal::scalar_product_op<T, boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > > > > >::value>::type>::type, const Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1> > Eigen::operator*(const T&, const StorageBaseType&) [with T = Eigen::Matrix<boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >, -1, -1>]’  
bug.cpp:8:9:   required from here  
/home/glisse/repos/multiprecision/include/boost/multiprecision/traits/is_byte_container.hpp:29:10: error: no type named ‘value_type’ in ‘struct std::iterator_traits<void>’  
   29 |    using container_value_type = typename std::remove_cv<typename std::iterator_traits<typename C::const_iterator>::value_type>::type;  
      |          ^~~~~~~~~~~~~~~~~~~~  
</pre>  
It looks like Eigen wants to test if the matrix product is convertible to a scalar, which tries to construct `cpp_int_backend` from something, one constructor uses is_byte_container to enable/disable itself, but is_byte_container does not seem particularly sfinae-friendly, which yields an error. The same code using Eigen compiles with `mpq_rational`.

---

## Comment 1

> Username: mekhontsev  
> Created at: 2021-11-28 15:58:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/393#issuecomment-981108938  

[https://gitlab.com/libeigen/eigen/-/issues/2356](https://gitlab.com/libeigen/eigen/-/issues/2356)

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-11-30 19:15:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/393#issuecomment-982939879  

Confirmed.
