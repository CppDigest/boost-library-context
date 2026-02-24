# #136 Use std type_traits instead of boost type_traits [Merged]

> Username: shreyans800755  
> Created at: 2017-06-24 20:40:00 UTC  
> Updated at: 2017-06-25 18:38:38 UTC  
> Merged at: 2017-06-25 18:17:42 UTC  
> Closed at: 2017-06-25 18:17:42 UTC  
> Url: https://github.com/boostorg/python/pull/136  

Fixes https://github.com/boostorg/python/issues/106

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2017-06-24 21:16:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/136#pullrequestreview-46133757  

This looks very promising. Thanks for working on this ! I have sprinkled a few inline comments. It would be great to complete this in time for the next release (I'd have to merge in the next couple of days for this).

📁 include/boost/python/detail/type_traits.hpp

```diff
   1 |+ // Copyright David Abrahams 2002.
```

> Username: stefanseefeld  
> Created_at: 2017-06-24 21:04:42 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123885450  

Shouldn't that be your name, and '2017' ? :-)

> Username: shreyans800755  
> Created_at: 2017-06-24 21:30:07 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123885866  

Ah yes! I just copied it from some other file blindly.

---

📁 include/boost/python/detail/type_traits.hpp

```diff
   5 |+ 
   6 |+ #ifndef BOOST_DETAIL_TYPE_TRAITS_HPP
   7 |+ # define BOOST_DETAIL_TYPE_TRAITS_HPP
```

> Username: stefanseefeld  
> Created_at: 2017-06-24 21:05:38 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123885466  

This should be BOOST_PYTHON_DETAIL_TYPE_TRAITS_HPP, to really match the complete filename.

---

📁 include/boost/python/detail/type_traits.hpp

```diff
  22 |+ # include <boost/type_traits/is_scalar.hpp>
  23 |+ # include <boost/type_traits/alignment_traits.hpp>
  24 |+ # include <boost/mpl/bool.hpp>
```

> Username: stefanseefeld  
> Created_at: 2017-06-24 21:06:58 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123885495  

Have you tried only conditionally including the above headers, using the same logic as below (i.e., if C++11 isn't supported) ? This would greatly reduce our dependency on Boost...


📁 include/boost/python/detail/value_is_xxx.hpp

```diff
  25 |+     typedef mpl::bool_<value> type;                                                            \
  26 |+                                                                                                \
  27 | };
```

> Username: stefanseefeld  
> Created_at: 2017-06-24 21:11:20 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123885582  

It looks like right now (i.e. even before your change) the `value_is_...` template is defined in the global namespace. Can it be moved into boost::python::detail ?


---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2017-06-25 03:20:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/136#pullrequestreview-46137426  

📁 include/boost/python/detail/type_traits.hpp

```diff
  26 |+ #else
  27 |+ # include <type_traits>
  28 |+ #endif
```

> Username: stefanseefeld  
> Created_at: 2017-06-25 03:20:45 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123889598  

Great, I hope with this change it builds on Travis-CI at least.


---

## Review 3 [Commented]

> Username: stefanseefeld  
> Created_at: 2017-06-25 12:46:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/136#pullrequestreview-46144750  

📁 include/boost/python/detail/value_is_xxx.hpp

```diff
  22 |+     BOOST_STATIC_CONSTANT(bool, value = is_##name<                                             \
  23 |+                                typename boost::python::detail::remove_cv<                      \
  24 |+                                   typename boost::python::detail::remove_reference<X_>::type   \
```

> Username: stefanseefeld  
> Created_at: 2017-06-25 12:46:48 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123898112  

Last minor nit-pick: now that this entire template is within the boost::python::detail namespace, it appears we don't need the qualification of `remove_cv` and `remove_reference` in the last two lines any longer.

> Username: shreyans800755  
> Created_at: 2017-06-25 13:14:07 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123898657  

I'll update it. Btw any idea, why appveyor is failing ?

> Username: stefanseefeld  
> Created_at: 2017-06-25 13:17:23 UTC  
> Updated_at: 2017-06-25 13:29:10 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123898732  

Great, thanks. Don't worry about appveyor, that's simply a problem with the testing logic (SCons). I'm in the process of substituting that by something else...

> Username: shreyans800755  
> Created_at: 2017-06-25 17:04:24 UTC  
> Url: https://github.com/boostorg/python/pull/136#discussion_r123903608  

Cool.


---
