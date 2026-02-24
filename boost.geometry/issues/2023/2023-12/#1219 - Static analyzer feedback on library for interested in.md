# #1219 - Static analyzer feedback on library for interested in. [Open]

> Username: dasfex  
> Created at: 2023-12-04 13:59:11 UTC  
> Updated at: 2023-12-04 14:30:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1219  

I ran PVS Studio analyzer on boost/geometry and get some interesting points on library code:  
```  
/boost/geometry/include/boost/geometry/policies/relate/direction.hpp	184	err	V583 The '?:' operator, regardless of its conditional expression, always returns one and the same value: -1.  
/boost/geometry/include/boost/geometry/formulas/karney_inverse.hpp	373	err	V547 Expression 'iteration < max_iterations' is always true.  
/boost/geometry/include/boost/geometry/algorithms/detail/overlay/get_turn_info_la.hpp	426	warn	V560 A part of conditional expression is always false: pk_q1 > 0.  
/boost/geometry/include/boost/geometry/algorithms/detail/overlay/get_turn_info_la.hpp	437	warn	V560 A part of conditional expression is always false: pk_q1 < 0.  
/boost/geometry/include/boost/geometry/algorithms/detail/within/multi_point.hpp	154	warn	V547 Expression 'in_val >= 0' is always true.  
/boost/geometry/include/boost/geometry/algorithms/detail/relate/areal_areal.hpp	233	warn	V649 There are two 'if' statements with identical conditional expressions. The first 'if' statement contains function return. This means that the second 'if' statement is senseless. Check lines: 220, 233.  
/boost/geometry/include/boost/geometry/algorithms/detail/relate/linear_areal.hpp	383	warn	V649 There are two 'if' statements with identical conditional expressions. The first 'if' statement contains function return. This means that the second 'if' statement is senseless. Check lines: 371, 383.  
/boost/geometry/include/boost/geometry/algorithms/detail/relate/linear_areal.hpp	420	warn	V649 There are two 'if' statements with identical conditional expressions. The first 'if' statement contains function return. This means that the second 'if' statement is senseless. Check lines: 407, 420.  
/boost/geometry/include/boost/geometry/algorithms/detail/relate/linear_linear.hpp	158	warn	V649 There are two 'if' statements with identical conditional expressions. The first 'if' statement contains function return. This means that the second 'if' statement is senseless. Check lines: 139, 158.  
/boost/geometry/include/boost/geometry/algorithms/detail/overlay/assign_parents.hpp	316	warn	V560 A part of conditional expression is always true: !is_dissolve.  
/boost/geometry/include/boost/geometry/algorithms/detail/is_simple/linear.hpp	73	err	V501 There are identical sub-expressions to the left and to the right of the '||' operator.  
/boost/geometry/include/boost/geometry/algorithms/detail/touches/implementation.hpp	176	warn	V1037 Two or more case-branches perform the same actions. Check lines: 176, 181  
```  
  
Maybe someone would be interested in researching of that cases and will make a fix.
