# #727 - MSVC Warning: error C4127: conditional expression is constant [Closed]

> Username: vschoech  
> Created at: 2020-06-17 09:28:45 UTC  
> Updated at: 2021-03-26 14:16:28 UTC  
> Closed at: 2020-06-19 17:12:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/727  

Compiling with boost 1.73.0, MSVC 2017 Version 15.8.0 with compiler options /W4 and /std:c++latest, I get "conditional expression is constant" in two places:  
```  
\boost_1_73_0\boost\geometry\algorithms\detail\overlay\follow.hpp(491): error C4127: conditional expression is constant  
\boost_1_73_0\boost\geometry\algorithms\detail\overlay\follow.hpp(491): note: consider using 'if constexpr' statement instead  
\boost_1_73_0\boost\geometry\algorithms\detail\overlay\intersection_insert.hpp(390): note: see reference to function template instantiation 'OutputIterator boost::geometry::detail::overlay::follow<GeometryOut,LineString,Areal,boost::geometry::overlay_difference,false,false>::apply<std::deque<turn_info,std::allocator<_Ty>>,OutputIterator,RobustPolicy,Strategy>(const LineString &,const Polygon &,boost::geometry::detail::overlay::operation_type,Turns &,const RobustPolicy &,OutputIterator,const Strategy &)' being compiled  
```  
and  
```  
\boost_1_73_0\boost\geometry\algorithms\detail\overlay\follow.hpp(523): error C4127: conditional expression is constant  
\boost_1_73_0\boost\geometry\algorithms\detail\overlay\follow.hpp(523): note: consider using 'if constexpr' statement instead  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2020-06-18 14:57:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/727#issuecomment-646075008  

> error C4127: conditional expression is constant  
  
Before the codebase switches to C++17, I'm afraid, there is no cleaner way to get rid of this warning than `#pragma warning(disable:4127)`

---

## Comment 2

> Username: tinko92  
> Created at: 2020-06-19 11:32:04 UTC  
> Updated at: 2020-06-19 11:34:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/727#issuecomment-646587027  

Just for consideration: If the FollowIsolatedPoints is the only problem here, something like the following might not be too intrusive:  
  
[constexpr_if_macro.patch.txt](https://github.com/boostorg/geometry/files/4804290/constexpr_if_macro.patch.txt)  
  
(TL;DR: the patch splits the condition and uses the BOOST_IF_CONSTEXPR macro for the constant expression)  
  
I admit that it is not very pretty, but it should get rid of the warning if I understand correctly (I don't have the proprietary software that was mentioned in the issue, so I can't test it myself). The other option I see would involve partial template specialization but that would probably require at least 10 lines of boilerplate, so that couldn't be considered clean either.

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-06-19 12:36:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/727#issuecomment-646612499  

Well, there is this macro:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/condition.hpp

---

## Comment 4

> Username: vschoech  
> Created at: 2021-03-26 14:16:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/727#issuecomment-808254810  

I can confirm that this warning no longer occurs with 1.75.0. Thank you!
