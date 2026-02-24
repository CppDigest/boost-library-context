# #7 - Add `BOOST_SCOPE_DETAIL_DOC_HIDDEN` to `unique_resource::reset` [Closed]

> Username: Ukilele  
> Created at: 2023-11-26 20:37:58 UTC  
> Updated at: 2023-11-28 12:10:35 UTC  
> Closed at: 2023-11-28 12:10:35 UTC  
> Url: https://github.com/boostorg/scope/issues/7  

Taking a look at the [documentation](https://lastique.github.io/scope/libs/scope/doc/html/boost/scope/unique_resource.html) as well as the source code of [unique_resource::reset](https://github.com/Lastique/scope/blob/develop/include/boost/scope/unique_resource.hpp#L1349), I recognized that the macro `BOOST_SCOPE_DETAIL_DOC_HIDDEN` is probably missing around the noexcept operator. Looking at the source code, the same might be true for the destructor of unique_resource, but this at least does not seem to have impact on the documentation. In the documentation, the destructor of unique_resource doesn't have any noexcept specifier at all.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-11-26 20:52:57 UTC  
> Url: https://github.com/boostorg/scope/issues/7#issuecomment-1826893610  

The reason why I used `BOOST_SCOPE_DETAIL_DOC_HIDDEN` to hide some `noexcept` arguments is that those arguments are involving non-public symbols (e.g. private types and types from `detail` namespace) and therefore should not be exposed in the documentation. This is not the case for `unique_resource::reset()` and the destructor.  
  
I suppose, I could hide those `noexcept` arguments too, if that makes the docs more readable.  
  
Regarding the missing `noexcept` on the destructor, that's just how Doxygen works, I can't do anything about it, unfortunately.

---

## Comment 2

> Username: Ukilele  
> Created at: 2023-11-27 07:56:51 UTC  
> Url: https://github.com/boostorg/scope/issues/7#issuecomment-1827315038  

Ah, I didn't know that it was intentional. As user I just see that most functions are `noexcept(...)` except `reset`, which is `noexcept(noexcept(std::declval< deleter_type & >()(std::declval< resource_type & >())))` so I thought it got forgotten there. I understand your reasoning. But yes, my personal preference would be to hide those, too.  
  
Thanks for your quick response and your explanations.
