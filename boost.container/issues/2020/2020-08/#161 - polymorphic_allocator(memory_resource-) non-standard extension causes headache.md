# #161 - polymorphic_allocator(memory_resource*) non-standard extension causes headache [Closed]

> Username: vinniefalco  
> Created at: 2020-08-29 20:48:19 UTC  
> Updated at: 2020-08-31 18:52:12 UTC  
> Closed at: 2020-08-31 11:37:19 UTC  
> Url: https://github.com/boostorg/container/issues/161  

This non-standard extension:  
https://github.com/boostorg/container/blob/5a52472cd00994bf1752e92c1c178a5a822a36cb/include/boost/container/pmr/polymorphic_allocator.hpp#L56  
  
Requires Boost.JSON users to link to Boost.Container even if they are not using the default resource (which they likely will never be). It also allows programs to rely on non-standard behavior, making them more difficult to port to C++17. I believe this non-standard extension should be removed. Construction of `polymorphic_allocator` with a null memory resource should throw something like `logic_error` or `invalid_parameter`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-08-31 11:31:12 UTC  
> Url: https://github.com/boostorg/container/issues/161#issuecomment-683723556  

Throwing an exception would be non-conforming, so the best option here is to remove the non-standard extension and add a simple assert in case a null pointer is passed.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-08-31 11:37:41 UTC  
> Url: https://github.com/boostorg/container/issues/161#issuecomment-683726202  

Extension removed, thanks for the report.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-31 14:53:17 UTC  
> Url: https://github.com/boostorg/container/issues/161#issuecomment-683829797  

Glad to help. Note that the upcoming Boost.JSON scheduled for formal review September 14-24 relies heavily on Boost.Container's `memory_resource` and `polymorphic_allocator` as building blocks for the discriminated union containers.  
  
What happens if users are depending on this extension? I thought throwing an exception (and causing their app to terminate) was preferable to getting possibly undefined behavior.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2020-08-31 18:49:55 UTC  
> Url: https://github.com/boostorg/container/issues/161#issuecomment-683963002  

The function was not correctly specified as it says (https://www.boost.org/doc/libs/1_74_0/doc/html/boost/container/pmr/polymorphic_allocator.html#idm45720956936592-bb):  
  
Requires: r is non-null.  
  
and the note:  
  
Non-standard extension: if r is null m_resource is set to get_default_resource().  
  
I think the removal will not cause much pain, as it was not clear if null was or not valid.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-08-31 18:52:12 UTC  
> Url: https://github.com/boostorg/container/issues/161#issuecomment-683964280  

Oh! Great! I didn't actually notice "Requires: r is non-null." when I looked at it :)
