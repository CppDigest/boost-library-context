# #416 - Compilation error with g++ [Closed]

> Username: thk686  
> Created at: 2017-08-17 23:28:48 UTC  
> Updated at: 2017-12-08 21:27:08 UTC  
> Closed at: 2017-12-08 21:27:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/416  

I am getting:  
  
```  
/usr/local/lib/R/site-library/BH/include/boost/mpl/assert.hpp:429:42: error: no   
matching function for call to ‘assertion_failed(mpl_::failed************ (boost:  
:geometry::index::detail::indexable<std::pair<Eigen::Matrix<double, 1, 1, 0, 1,   
1>, double>, false>::NOT_VALID_INDEXABLE_TYPE::************)(Eigen::Matrix<doubl  
e, 1, 1, 0, 1, 1>))’  
     , BOOST_PP_CAT(mpl_assertion_in_line_,counter) = sizeof( \  
                                                            ~~~  
         boost::mpl::assertion_failed<(c)>( BOOST_PP_CAT(mpl_assert_arg,counter)  
::assert_arg() ) \  
         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
~~~~~~~~~~~~~~~~~~  
         ) \  
         ~                                   
  
```  
with g++ (6.3). This compiles fine with Clang++ (8.1). I am registering a matrix from the Eigen library as the coordinate type. The header look like:  
  
```  
// Boost.Geometry (aka GGL, Generic Geometry Library)  
  
// Copyright (c) 2010 Alfredo Correa  
// Copyright (c) 2010-2012 Barend Gehrels, Amsterdam, the Netherlands.  
// Copyright (c) 2016 Norbert Wenzel  
  
// Use, modification and distribution is subject to the Boost Software License,  
// Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt)  
  
// Modified from the orignal code by Tim Keitt 4/7/17  
// Changes Copyright (c) 2017 Tim Keitt  
  
#ifndef BOOST_GEOMETRY_GEOMETRIES_ADAPTED_EIGEN_MATRIX_HPP  
#define BOOST_GEOMETRY_GEOMETRIES_ADAPTED_EIGEN_MATRIX_HPP  
  
#define BOOST_GEOMETRY_ADAPTED_EIGEN_MATRIX_TAG_DEFINED  
  
#include <cstddef>  
  
#include <boost/type_traits/is_arithmetic.hpp>  
  
#include <boost/geometry/core/access.hpp>  
#include <boost/geometry/core/cs.hpp>  
#include <boost/geometry/core/coordinate_dimension.hpp>  
#include <boost/geometry/core/coordinate_type.hpp>  
#include <boost/geometry/core/tags.hpp>  
  
#include <Eigen/Dense>  
  
namespace boost { namespace geometry  
{  
  
  
#ifndef DOXYGEN_NO_TRAITS_SPECIALIZATIONS  
namespace traits  
{  
  
  
#ifndef DOXYGEN_NO_DETAIL  
namespace detail  
{  
  
  
// Create class and specialization to indicate the tag  
// for normal cases and the case that the type of the std-array is arithmetic  
template <bool>  
struct eigen_matrix_tag  
{  
  typedef geometry_not_recognized_tag type;  
};  
  
  
template <>  
struct eigen_matrix_tag<true>  
{  
  typedef point_tag type;  
};  
  
  
} // namespace detail  
#endif // DOXYGEN_NO_DETAIL  
  
  
// Assign the point-tag, preventing arrays of points getting a point-tag  
template <typename CoordinateType, std::size_t DimensionCount>  
struct tag<Eigen::Matrix<CoordinateType, DimensionCount, 1>>  
  : detail::eigen_matrix_tag<boost::is_arithmetic<CoordinateType>::value> {};  
  
  
template <typename CoordinateType, std::size_t DimensionCount>  
struct coordinate_type<Eigen::Matrix<CoordinateType, DimensionCount, 1>>  
{  
  typedef CoordinateType type;  
};  
  
  
template <typename CoordinateType, std::size_t DimensionCount>  
struct dimension<Eigen::Matrix<CoordinateType, DimensionCount, 1>>: boost::mpl::int_<DimensionCount> {};  
  
  
template <typename CoordinateType, std::size_t DimensionCount, std::size_t Dimension>  
struct access<Eigen::Matrix<CoordinateType, DimensionCount, 1>, Dimension>  
{  
  static inline CoordinateType get(Eigen::Matrix<CoordinateType, DimensionCount, 1> const& a)  
  {  
    return a(Dimension);  
  }  
  
  static inline void set(Eigen::Matrix<CoordinateType, DimensionCount, 1>& a,  
                         CoordinateType const& value)  
  {  
    a(Dimension) = value;  
  }  
};  
  
  
} // namespace traits  
#endif // DOXYGEN_NO_TRAITS_SPECIALIZATIONS  
  
  
}} // namespace boost::geometry  
  
  
#define BOOST_GEOMETRY_REGISTER_EIGEN_MATRIX_CS(CoordinateSystem) \  
namespace boost { namespace geometry { namespace traits {      \  
template <class T, std::size_t N>                              \  
struct coordinate_system<Eigen::Matrix<T, N, 1>>               \  
{                                                              \  
  typedef CoordinateSystem type;                               \  
};                                                             \  
}}}  
  
  
#endif // BOOST_GEOMETRY_GEOMETRIES_ADAPTED_EIGEN_MATRIX_HPP  
  
```  
The BH R package uses Boost 1.62.  
  
THK

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-08-18 23:23:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/416#issuecomment-323481694  

Ok, so you adapt `Eigen::Matrix` to Boost.Geometry Point concept. The code above looks ok.  
  
What's the program you're compiling? I see that you're creating R-tree and passing `std::pair<Eigen::Matrix<...>, double>` as ValueType. Is that correct? Or are you calling some algorithm?  
  
The `NOT_VALID_INDEXABLE_TYPE` MPL assertion occurs when `tag<ValueType>::type` is neither point_tag, box_tag nor segment_tag. Could you check in your code if the tag is set properly? E.g.: `std::cout << boost::is_same<bg::tag<Eigen::Matrix<...>>::type, point_tag>::value;`.  
  
How are you including Boost.Geometry in your code and what is the order of includes? It's possible that the order of `#includes` is important for GCC. This wouldn't be the first time something like this happen with this compiler.

---

## Comment 2

> Username: thk686  
> Created at: 2017-12-05 04:28:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/416#issuecomment-349192010  

```  
// [[Rcpp::export]]  
void test_tag()  
{  
  Rcout << boost::is_same<bg::tag<Eigen::Matrix<double, 2, 1>>::type, bg::point_tag>::value << std::endl;  
}  
```  
Returns 1.

---

## Comment 3

> Username: thk686  
> Created at: 2017-12-05 04:35:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/416#issuecomment-349192936  

Tried moving header to first and last in source file without luck.

---

## Comment 4

> Username: awulkiew  
> Created at: 2017-12-05 13:08:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/416#issuecomment-349299233  

This may be a problem with wrong traits specializations. If `Eigen::Matrix` rows, cols, options etc. are `int`, not `std::size_t` then `DimensionCount` in specializations should be of type `int` too.  
  
Btw, I see that `Eigen::Matrix` is defined as:  
  
    Matrix<typename Scalar,  
       int RowsAtCompileTime,  
       int ColsAtCompileTime,  
       int Options = 0,  
       int MaxRowsAtCompileTime = RowsAtCompileTime,  
       int MaxColsAtCompileTime = ColsAtCompileTime>  
  
Maybe this particular compiler would prefer to have all 6 template parameters in specializations? You pass only 3.  
  
Ok, so `bg::tag` seems to work. How about some simple algorithm? Does `distance(p1, p2)` or `disjoint(p1, p2)` compile and generate correct result?

---

## Comment 5

> Username: thk686  
> Created at: 2017-12-08 20:28:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/416#issuecomment-350364128  

It looks like changing the Eigen template types from size_t to int fixes  
the problem.  
  
THK  
  
On Tue, Dec 5, 2017 at 7:09 AM, Adam Wulkiewicz <notifications@github.com>  
wrote:  
  
> This may be a problem with wrong traits specializations. If Eigen::Matrix  
> rows, cols, options etc. are int, not std::size_t then DimensionCount in  
> specializations should be of type int too.  
>  
> Btw, I see that Eigen::Matrix is defined as:  
>  
> Matrix<typename Scalar,  
>    int RowsAtCompileTime,  
>    int ColsAtCompileTime,  
>    int Options = 0,  
>    int MaxRowsAtCompileTime = RowsAtCompileTime,  
>    int MaxColsAtCompileTime = ColsAtCompileTime>  
>  
> Maybe this particular compiler would prefer to have all 6 template  
> parameters in specializations? You pass only 3.  
>  
> Ok, so bg::tag seems to work. How about some simple algorithm? Does distance(p1,  
> p2) or disjoint(p1, p2) compile and generate correct result?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/geometry/issues/416#issuecomment-349299233>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAkLyXcS9j9GpitNVupwsbbOlMQqH_PAks5s9UBsgaJpZM4O67mU>  
> .  
>

---

## Comment 6

> Username: awulkiew  
> Created at: 2017-12-08 21:27:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/416#issuecomment-350376215  

Ok, great.
