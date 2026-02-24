# #759 Replace MPL_ASSERT with std static_assert. [Merged]

> Username: awulkiew  
> Created at: 2020-10-01 22:52:58 UTC  
> Updated at: 2020-10-15 23:35:56 UTC  
> Merged at: 2020-10-15 23:03:35 UTC  
> Closed at: 2020-10-15 23:03:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/759  

This PR replaces `MPL_ASSERT` and `MPL_ASSERT_MSG` with `BOOST_GEOMETRY_STATIC_ASSERT` or `BOOST_GEOMETRY_STATIC_ASSERT_FALSE` expanded to `static_assert`.  
  
`not_implemented` now takes parameter pack so can take arbitrary number of types.  
  
It also removes `bg::info::XXX` from `not_implemented` because IMO this does not really enhance the output.  
  
Below are examples showing how the compiler output is changed by this PR:  
  
MSVC (`MPL_ASSERT` and `bg::info::XXX`):  
```  
1>  d:\lib\modular-boost\libs\geometry\include\boost\geometry\algorithms\not_implemented.hpp(70): error C2664: 'int boost::mpl::assertion_failed<false>(boost::mpl::assert<false>::type)': cannot convert argument 1 from 'boost::mpl::failed ************(__cdecl boost::geometry::nyi::not_implemented_error<boost::geometry::info::LINESTRING,boost::geometry::info::MULTI_POLYGON,boost::geometry::info::POLYGON>::THIS_OPERATION_IS_NOT_OR_NOT_YET_IMPLEMENTED::* ***********)(boost::mpl::assert_::types<Term1,Term2,Term3,boost::mpl::na>)' to 'boost::mpl::assert<false>::type'  
1>          with  
1>          [  
1>              Term1=boost::geometry::info::LINESTRING,  
1>              Term2=boost::geometry::info::MULTI_POLYGON,  
1>              Term3=boost::geometry::info::POLYGON  
1>          ]  
1>  d:\lib\modular-boost\libs\geometry\include\boost\geometry\algorithms\not_implemented.hpp(77): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
1>  d:\lib\modular-boost\libs\geometry\include\boost\geometry\algorithms\not_implemented.hpp(126): note: see reference to class template instantiation 'boost::geometry::nyi::not_implemented_error<boost::geometry::info::LINESTRING,boost::geometry::info::MULTI_POLYGON,boost::geometry::info::POLYGON>' being compiled  
1>  d:\lib\modular-boost\libs\geometry\include\boost\geometry\algorithms\union.hpp(55): note: see reference to class template instantiation 'boost::geometry::not_implemented<TagIn1,TagIn2,TagOut>' being compiled  
1>          with  
1>          [  
1>              TagIn1=boost::geometry::linestring_tag,  
1>              TagIn2=boost::geometry::multi_polygon_tag,  
1>              TagOut=boost::geometry::polygon_tag  
1>          ]  
  
(...)  
```  
  
MSVC (`static_assert`):  
```  
1>  d:\lib\modular-boost\libs\geometry\include\boost\geometry\algorithms\not_implemented.hpp(64): error C2338: This operation is not or not yet implemented  
1>  d:\lib\modular-boost\libs\geometry\include\boost\geometry\algorithms\not_implemented.hpp(98): note: see reference to class template instantiation 'boost::geometry::nyi::not_implemented_error<TagIn1,TagIn2,TagOut>' being compiled  
1>          with  
1>          [  
1>              TagIn1=boost::geometry::linestring_tag,  
1>              TagIn2=boost::geometry::multi_polygon_tag,  
1>              TagOut=boost::geometry::polygon_tag  
1>          ]  
1>  d:\lib\modular-boost\libs\geometry\include\boost\geometry\algorithms\union.hpp(55): note: see reference to class template instantiation 'boost::geometry::not_implemented<TagIn1,TagIn2,TagOut>' being compiled  
1>          with  
1>          [  
1>              TagIn1=boost::geometry::linestring_tag,  
1>              TagIn2=boost::geometry::multi_polygon_tag,  
1>              TagOut=boost::geometry::polygon_tag  
1>          ]  
  
(...)  
```  
  
GCC (`MPL_ASSERT` and `bg::info::XXX`):  
```  
..\../boost/geometry/algorithms/not_implemented.hpp: In instantiation of 'struct boost::geometry::nyi::not_implemented_error<boost::geometry::info::LINESTRING, boost::geometry::info::MULTI_POLYGON, boost::geometry::info::POLYGON>':  
..\../boost/geometry/algorithms/not_implemented.hpp:109:8:   required from 'struct boost::geometry::not_implemented<boost::geometry::linestring_tag, boost::geometry::multi_polygon_tag, boost::geometry::polygon_tag>'  
  
(...)  
  
..\../boost/mpl/assert.hpp:440:42: error: no matching function for call to 'assertion_failed<false>(mpl_::failed************ (boost::geometry::nyi::not_implemented_error<boost::geometry::info::LINESTRING, boost::geometry::info::MULTI_POLYGON, boost::geometry::info::POLYGON>::THIS_OPERATION_IS_NOT_OR_NOT_YET_IMPLEMENTED::************)(mpl_::assert_::types<boost::geometry::info::LINESTRING, boost::geometry::info::MULTI_POLYGON, boost::geometry::info::POLYGON, mpl_::na>))'  
     , BOOST_PP_CAT(mpl_assertion_in_line_,counter) = sizeof( \  
                                                            ~~~  
         boost::mpl::assertion_failed<(c)>( BOOST_PP_CAT(mpl_assert_arg,counter)::assert_arg() ) \  
         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
         ) \  
         ~  
..\../boost/mpl/assert.hpp:60:58: note: in definition of macro 'BOOST_MPL_AUX_ASSERT_CONSTANT'  
 #   define BOOST_MPL_AUX_ASSERT_CONSTANT(T, expr) enum { expr }  
                                                          ^~~~  
..\../boost/mpl/assert.hpp:455:1: note: in expansion of macro 'BOOST_MPL_ASSERT_MSG_IMPL'  
 BOOST_MPL_ASSERT_MSG_IMPL( BOOST_MPL_AUX_PP_COUNTER(), c, msg, types_ ) \  
 ^~~~~~~~~~~~~~~~~~~~~~~~~  
..\../boost/geometry/algorithms/not_implemented.hpp:70:5: note: in expansion of macro 'BOOST_MPL_ASSERT_MSG'  
     BOOST_MPL_ASSERT_MSG  
     ^~~~~~~~~~~~~~~~~~~~  
..\../boost/mpl/assert.hpp:83:5: note: candidate: 'template<bool C> int mpl_::assertion_failed(typename mpl_::assert<C>::type)'  
 int assertion_failed( typename assert<C>::type );  
     ^~~~~~~~~~~~~~~~  
..\../boost/mpl/assert.hpp:83:5: note:   template argument deduction/substitution failed:  
..\../boost/mpl/assert.hpp:440:92: note:   cannot convert 'boost::geometry::nyi::not_implemented_error<boost::geometry::info::LINESTRING, boost::geometry::info::MULTI_POLYGON, boost::geometry::info::POLYGON>::THIS_OPERATION_IS_NOT_OR_NOT_YET_IMPLEMENTED77::assert_arg()' (type 'mpl_::failed************ (boost::geometry::nyi::not_implemented_error<boost::geometry::info::LINESTRING, boost::geometry::info::MULTI_POLYGON, boost::geometry::info::POLYGON>::THIS_OPERATION_IS_NOT_OR_NOT_YET_IMPLEMENTED::************)(mpl_::assert_::types<boost::geometry::info::LINESTRING, boost::geometry::info::MULTI_POLYGON, boost::geometry::info::POLYGON, mpl_::na>)') to type 'mpl_::assert<false>::type' {aka 'mpl_::assert<false>'}  
         boost::mpl::assertion_failed<(c)>( BOOST_PP_CAT(mpl_assert_arg,counter)::assert_arg() ) \  
                                                                                            ^  
..\../boost/mpl/assert.hpp:60:58: note: in definition of macro 'BOOST_MPL_AUX_ASSERT_CONSTANT'  
 #   define BOOST_MPL_AUX_ASSERT_CONSTANT(T, expr) enum { expr }  
                                                          ^~~~  
..\../boost/mpl/assert.hpp:455:1: note: in expansion of macro 'BOOST_MPL_ASSERT_MSG_IMPL'  
 BOOST_MPL_ASSERT_MSG_IMPL( BOOST_MPL_AUX_PP_COUNTER(), c, msg, types_ ) \  
 ^~~~~~~~~~~~~~~~~~~~~~~~~  
..\../boost/geometry/algorithms/not_implemented.hpp:70:5: note: in expansion of macro 'BOOST_MPL_ASSERT_MSG'  
     BOOST_MPL_ASSERT_MSG  
     ^~~~~~~~~~~~~~~~~~~~  
```  
  
GCC (`static_assert`):  
```  
..\../boost/geometry/algorithms/not_implemented.hpp: In instantiation of 'struct boost::geometry::nyi::not_implemented_error<boost::geometry::linestring_tag, boost::geometry::multi_polygon_tag, boost::geometry::polygon_tag>':  
..\../boost/geometry/algorithms/not_implemented.hpp:94:8:   required from 'struct boost::geometry::not_implemented<boost::geometry::linestring_tag, boost::geometry::multi_polygon_tag, boost::geometry::polygon_tag>'  
  
(...)  
  
..\../boost/geometry/core/static_assert.hpp:25:15: error: static assertion failed: This operation is not or not yet implemented  
 static_assert(::boost::geometry::detail::static_assert_check<(CHECK), __VA_ARGS__>::value, MESSAGE)  
               ^~  
..\../boost/geometry/core/static_assert.hpp:25:15: note: in definition of macro 'BOOST_GEOMETRY_STATIC_ASSERT'  
 static_assert(::boost::geometry::detail::static_assert_check<(CHECK), __VA_ARGS__>::value, MESSAGE)  
```

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-02 12:16:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/759#pullrequestreview-501083724  

📁 include/boost/geometry/algorithms/detail/assign_values.hpp

```diff
 256 |+     BOOST_GEOMETRY_STATIC_ASSERT_FALSE(
 257 |+         "Not or not yet implemented for this Geometry type.",
 258 |+         GeometryTag, Geometry, std::integer_sequence<std::size_t, DimensionCount>);
```

> Username: awulkiew  
> Created_at: 2020-10-02 12:16:57 UTC  
> Updated_at: 2020-10-15 22:31:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#discussion_r498784811  

Here I should've used `integral_constant`.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-10-02 15:05:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#issuecomment-702787568  

Btw, this PR also decreases the amount of code included from Boost.Range by including only the headers that are really needed.

---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2020-10-02 17:25:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/759#pullrequestreview-501309122  

Thanks! I glanced through most of it and it looks good to me.

📁 include/boost/geometry/algorithms/assign.hpp

```diff
 240 |+             same_point_order,
 241 |+             "Assign is not supported for different point orders.",
 242 |+             Geometry1, Geometry2);
```

> Username: barendgehrels  
> Created_at: 2020-10-02 17:13:09 UTC  
> Updated_at: 2020-10-15 22:31:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#discussion_r498947082  

This style looks really good to me.

> Username: awulkiew  
> Created_at: 2020-10-15 22:43:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#discussion_r505904994  

Yes, this is possible thanks to variable-argument macros added in C++11.  
  
Btw, have in mind that `static_assert` is slightly different than `BOOST_MPL_ASSERT`. A condition used in it has to be dependent on a template parameter. Otherwise the `static_assert` will always fail or pass even if a template is not instantiated. E.g. a program like this:  
```  
template <typename T>  
struct A  
{  
    static_assert(false);  
};  
  
int main()  
{}  
```  
will always fail to compile. This is why I also added `BOOST_GEOMETRY_STATIC_ASSERT_FALSE` which requires dependent types and deals with this problem.


📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
  19 |+ #include <boost/range/end.hpp>
  20 |+ #include <boost/range/size.hpp>
  21 |+ #include <boost/range/value_type.hpp>
```

> Username: barendgehrels  
> Created_at: 2020-10-02 17:13:42 UTC  
> Updated_at: 2020-10-15 22:31:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#discussion_r498947343  

Top, I really appreciate this. We encountered it too.


📁 include/boost/geometry/algorithms/not_implemented.hpp

```diff
 124 |-               >::type
 125 |-       >
  60 |+       //nyi::not_implemented_error<typename nyi::tag_to_term<Terms>::type...>
```

> Username: barendgehrels  
> Created_at: 2020-10-02 17:15:45 UTC  
> Updated_at: 2020-10-15 22:31:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#discussion_r498948316  

Can this line go?

> Username: awulkiew  
> Created_at: 2020-10-15 22:43:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#discussion_r505905058  

Done.


📁 include/boost/geometry/core/coordinate_dimension.hpp

```diff
 102 | {
 106 |-     BOOST_STATIC_ASSERT(( static_cast<int>(dimension<Geometry>::value) == Dimensions ));
 103 |+     BOOST_STATIC_ASSERT(( dimension<Geometry>::value == Dimensions ));
```

> Username: barendgehrels  
> Created_at: 2020-10-02 17:16:20 UTC  
> Updated_at: 2020-10-15 22:31:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#discussion_r498948630  

Is `GEOMETRY` missing here?

> Username: awulkiew  
> Created_at: 2020-10-03 15:00:35 UTC  
> Updated_at: 2020-10-15 22:31:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#discussion_r499155060  

This is the `static_assert` implemented outside Geometry. It only requires the condition while ours requires message and types. Here the condition is used as the message. I left it as it was because I was only getting rid of `MPL_ASSERT`. `BOOST_STATIC_ASSERT` expands to `static_assert` in C++11.


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2020-10-05 09:42:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/759#pullrequestreview-501886818  

Looks good to me, thanks!

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-10-15 22:43:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/759#issuecomment-709628491  

Thank you for the reviews.

---
