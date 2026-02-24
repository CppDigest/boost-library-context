# #160 - Operator traits documentation does not mention C++11 implementation [Closed]

> Username: eldiener  
> Created at: 2021-06-17 06:17:53 UTC  
> Updated at: 2022-11-21 18:24:15 UTC  
> Closed at: 2022-11-21 18:24:15 UTC  
> Url: https://github.com/boostorg/type_traits/issues/160  

Looking at the general documentation for the operator traits at boost_typetraits/category/value_traits/operators.html one would think that the functionality could better be done using C++11 decltype/declval/void_t with the "Known issues" section possibly being moot in that case. Then I realized when reading the individual documentation for particular operator traits that indeed for supported Boost.Config functionality the C++11 decltype/declval/void_t technique is indeed being used and that the "Known Issues" in the general documentation for compilers in C++11 on up mode is indeed moot. So the general documentation has reams of stuff about implementation of operator trraits and known issues that is only true for C++03 compilers and that is completely false for C++11 on up compilers.  
  
I appreciate greatly the work Frederick Bron did for operator traits, but should not the general operator traits documentation be updated to refelect the actuality of the C++11 on up implementation. It certainly misled me.
