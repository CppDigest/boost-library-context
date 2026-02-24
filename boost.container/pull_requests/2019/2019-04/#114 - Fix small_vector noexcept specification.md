# #114 Fix small_vector noexcept specification [Closed]

> Username: Lastique  
> Created at: 2019-04-13 21:18:04 UTC  
> Updated at: 2019-04-26 23:52:24 UTC  
> Closed at: 2019-04-26 21:26:30 UTC  
> Url: https://github.com/boostorg/container/pull/114  

1. Move `small_vector_base` forward-declaration to `container_fwd.hpp`. This is useful if the user only wants to use `small_vector_base` in its interface headers.  
2. Fix `small_vector` default constructor `noexcept` specification.  
  
The default constructor used to incorrectly apply `is_nothrow_default_constructible` trait to the template argument of the `small_vector` class template, which is `void` by default. The fix is to apply the trait to the actual allocator type.  
  
Next is the fix for `noexcept` specifications of the `small_vector_allocator` class members, including the default constructor, which was previously not declared and a non-`noexcept` initializing constructor was used in its stead. The fix is to explicitly specify the default constructor with a proper `noexcept` specification. While at it, other constructors and assignment operators were also fixed with proper `noexcept` specifications.  
  
As a result, `small_vector` default constructor `noexcept` specification now properly reflects whether the default constructor of the allocator is `noexcept`. This is useful if user's class has `small_vector` members and attempts to request a `noexcept` defaulted default constructor.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-04-26 17:30:12 UTC  
> Url: https://github.com/boostorg/container/pull/114#issuecomment-487137606  

Thanks for the patch. Commit 5ec7aa7 cherry-picked to develop. Investigating why continuous integration fails with the noexcept specificcation.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2019-04-26 17:51:54 UTC  
> Url: https://github.com/boostorg/container/pull/114#issuecomment-487144575  

Reviewing the patch, some comments:  
  
- Cpp17Allocator requirements require noexcept for allocator move and copy constructors. This means that looking for dtl::is_nothrow_move_constructible<allocator_type>::value and similar is redundant.  
  
- I'm unsure about marking some constructors conditionally noexcept (like the variadic constructor). It complicates code a lot and I don't think performance would improve. Not sure also if the variadic constructor is needed at all, I think it is not used, I will review the use cases.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-04-26 19:05:02 UTC  
> Url: https://github.com/boostorg/container/pull/114#issuecomment-487167496  

> Cpp17Allocator requirements require noexcept for allocator move and copy constructors. This means that looking for dtl::is_nothrow_move_constructible<allocator_type>::value and similar is redundant.  
  
I didn't know allocator copy and move are required to be `noexcept`. Not sure if every allocator in the wild follows this requirement, though, so I tend to prefer the traits. But I can change to `BOOST_NOEXCEPT` no problem, if you want me to.  
  
> I'm unsure about marking some constructors conditionally noexcept (like the variadic constructor).  
  
I'd like the constructors to be as transparent as possible.  
  
Also, I seem to remember that at least some compilers prefer the templated constructors to non-templated ones like copy constructors in some cases. I think, it was when you construct a copy from a non-const allocator variable, but maybe there were other cases. For this reason, the templated constructor should have the same effect, including `noexcept` specification, as the non-templated one.

---

## Comment 4

> Username: Lastique  
> Created_at: 2019-04-26 19:11:48 UTC  
> Url: https://github.com/boostorg/container/pull/114#issuecomment-487169185  

I fixed a typo in the macro and rebased to the current develop. This should fix C++03 tests.

---

## Comment 5

> Username: igaztanaga  
> Created_at: 2019-04-26 21:28:58 UTC  
> Url: https://github.com/boostorg/container/pull/114#issuecomment-487207305  

Thanks. All other containers assume allocator copy/move don't throw, so I've modified your patch to inconditionally make some operations noexcept. The variadic constructor was not needed at all so I've removed it, smalLvector_allocator is an internal class so no one should notice this.

---
