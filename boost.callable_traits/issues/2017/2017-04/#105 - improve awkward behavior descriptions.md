# #105 - [review] improve awkward behavior descriptions [Closed]

> Username: badair  
> Created at: 2017-04-09 03:26:13 UTC  
> Updated at: 2017-07-02 20:38:37 UTC  
> Closed at: 2017-07-02 20:38:37 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/105  

> The description of behavior seems a bit awkward due to the use of the passive tense: "std::false_type is inherited by is_lvalue_reference_member<T> and is aliased by typename is_lvalue_reference_member<T>::type, except when one of the following criteria is met, in which case std::true_type would be similarly inherited and aliased". Examining the implementation, it is unclear  
why the repeated alias declaration is necessary when false/true_type already define a member typedef 'type' that is itself.  
  
from http://lists.boost.org/Archives/boost/2017/04/234173.php
