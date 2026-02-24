# #106 - [review] handle top-level CV [Closed]

> Username: badair  
> Created at: 2017-04-09 03:27:09 UTC  
> Updated at: 2017-11-12 19:59:47 UTC  
> Closed at: 2017-11-12 19:59:47 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/106  

> I see no reason why, say, is_volatile_member<int (foo::*)() volatile> is true but not is_volatile_member<int (foo::* const)() volatile>  
  
from http://lists.boost.org/Archives/boost/2017/04/234173.php

---

## Comment 1

> Username: badair  
> Created at: 2017-04-09 04:07:41 UTC  
> Updated at: 2017-04-09 04:07:51 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/106#issuecomment-292762658  

one annoying aspect of this is that CV must be added back for the metafunctions (e.g. `boost::copy_cv`)

---

## Comment 2

> Username: badair  
> Created at: 2017-11-04 20:34:16 UTC  
> Updated at: 2017-11-12 19:57:36 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/106#issuecomment-341927934  

Implemented for return_type, args, class_of, function_type, and all is_*/has_* traits (except is_invocable). Implementing this for is_invocable is tricky because the result depends on the cv-ref-ness of the type when the type is a function object. The other traits would require copying cv/ref from the template argument to the result, but I don't think this is very useful (TODO - implement or document this prior to Boost release) (Edit: done).  
  
Also TODO implement this for qualified_class_of before Boost release (this was an oversight on my part)  (Edit: done).

---

## Comment 3

> Username: badair  
> Created at: 2017-11-12 19:59:47 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/106#issuecomment-343762868  

add_foo/remove_foo will not support cv/ref qualified pointers. I spent a few hours working on this last night and it proved more difficult than I expected. Closing for now, but it would be possible to pick this up again in the future.
