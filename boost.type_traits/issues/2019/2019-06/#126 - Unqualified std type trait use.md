# #126 - Unqualified std type trait use? [Open]

> Username: uecasm  
> Created at: 2019-06-19 02:17:35 UTC  
> Updated at: 2019-06-19 16:47:03 UTC  
> Url: https://github.com/boostorg/type_traits/issues/126  

https://github.com/boostorg/type_traits/blob/efc0be6b55672720da464a923f97bc77b48117c8/include/boost/type_traits/intrinsics.hpp#L228-L235  
  
When I compile this with clang-cl on Windows, I get the following error (in Boost 1.68, but the code appears unchanged here):  
  
    T:\boost\boost_1_68_0\include\boost/type_traits/has_trivial_move_assign.hpp(49): error : no template named 'is_assignable'; did you mean 'std::is_assignable'?  
    T:\boost\boost_1_68_0\include\boost/type_traits/intrinsics.hpp(233): note: expanded from macro 'BOOST_HAS_TRIVIAL_MOVE_ASSIGN'  
  
It does seem peculiar that this is being called unqualified here.

---

## Comment 1

> Username: uecasm  
> Created at: 2019-06-19 02:22:06 UTC  
> Updated at: 2019-06-19 02:23:22 UTC  
> Url: https://github.com/boostorg/type_traits/issues/126#issuecomment-503379789  

Perhaps the intent is to use the boost traits instead, but in that case the `::boost::` namespace must be explicitly specified, since it's in a macro context and can't assume that it's being used inside the `boost` namespace.  
  
Most of the clang-specific macro definitions in this area appear to have the same problem, although oddly these particular ones appear to do it both ways for some reason.

---

## Comment 2

> Username: uecasm  
> Created at: 2019-06-19 02:34:50 UTC  
> Url: https://github.com/boostorg/type_traits/issues/126#issuecomment-503382096  

Although while the above is definitely a bug in its own right, possibly of note is that the location that actually flagged the error to me was this:  
  
https://github.com/boostorg/type_traits/blob/efc0be6b55672720da464a923f97bc77b48117c8/include/boost/type_traits/has_trivial_move_assign.hpp#L49  
  
Which does happen to be in the `boost` namespace.  Which suggests that there's an additional problem of a missing `#include` dependency.  Though it looks like ad326841ecca3e1a31 fixed that particular issue.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-06-19 11:02:13 UTC  
> Url: https://github.com/boostorg/type_traits/issues/126#issuecomment-503513354  

This should have been fixed in https://github.com/boostorg/type_traits/commit/ad326841ecca3e1a31102d5ddaf4e82f55a13742#diff-6f32db8e7667635f00953149c5f40853.  
  
Can you please update to 1.70 and report back?

---

## Comment 4

> Username: uecasm  
> Created at: 2019-06-19 12:54:40 UTC  
> Url: https://github.com/boostorg/type_traits/issues/126#issuecomment-503548192  

Did you see my last comment?  I acknowledged that.  
  
The macros still have public names (even if perhaps they are not intended for public use -- in which case perhaps they should be renamed), and in their current form they are implemented inconsistently and won't work if used outside of the `boost` namespace.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-06-19 16:47:02 UTC  
> Url: https://github.com/boostorg/type_traits/issues/126#issuecomment-503640522  

The macros are not intended for use anywhere except inside the type traits library.  
  
Yes, they are documented: but that's so that folks can implement them for compilers about which we currently know nothing.  Which is to say they are a customisation point for type_traits, but NOT user usable.  
  
If you wish to submit a PR to fix the perceived issue that's fine, but unless you have a use case where Boost-1.70 fails, I see no need to chase after this myself.
