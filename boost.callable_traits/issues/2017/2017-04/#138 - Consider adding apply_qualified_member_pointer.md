# #138 - Consider adding apply_qualified_member_pointer [Open]

> Username: ldionne  
> Created at: 2017-04-17 03:57:26 UTC  
> Updated at: 2017-04-18 01:00:06 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/138  

From https://lists.boost.org/Archives/boost/2017/04/234374.php:  
  
> It would also _probably_ make sense to provide  `apply_qualified_member_pointer`, which, given `void(float)` and `foo const&`, would yield `void (foo::*)(float) const&`.
