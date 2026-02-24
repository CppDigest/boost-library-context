# #139 - Warnings after applying nodiscard to operators [Open]

> Username: uecasm  
> Created at: 2020-02-17 07:02:24 UTC  
> Updated at: 2020-02-17 07:02:24 UTC  
> Url: https://github.com/boostorg/type_traits/issues/139  

I encountered this in Boost 1.68; I have not verified it in current Boost but I did manually inspect the relevant code and it appears to be unchanged, so I suspect the issue is still present.  
  
If you apply the `[[nodiscard]]` attribute to various operators (such as `operator==` and `operator+`) -- as seems sensible as these are pure functions -- then VS2017 at least produces a warning that the return value is discarded:  
  
```  
t:\boost\boost_1_68_0\include\boost\type_traits\detail\has_binary_operator.hpp(149): warning C4834: discarding return value of function with 'nodiscard' attribute  
t:\boost\boost_1_68_0\include\boost\type_traits\detail\has_binary_operator.hpp(233): note: see reference to class template instantiation 'boost::detail::has_equal_to_impl::operator_returns_void<Lhs,Rhs>' being compiled  
```  
  
(In my case this originated from a call to `boost::has_equal_to` and `boost::has_less` (and thus `operator==` and `operator<`), but it would presumably affect any other binary operator, and most likely other operator types as well.)  
  
Most likely this just needs an explicit cast to `void` to silence the warning.
