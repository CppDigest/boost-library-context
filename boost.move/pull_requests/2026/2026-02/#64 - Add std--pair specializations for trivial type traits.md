# #64 Add std::pair specializations for trivial type traits [Closed]

> Username: janzizazizka  
> Created at: 2026-02-09 09:58:06 UTC  
> Updated at: 2026-02-16 21:42:52 UTC  
> Closed at: 2026-02-16 21:42:52 UTC  
> Url: https://github.com/boostorg/move/pull/64  

Move the std::pair<A,B> partial specializations for the following traits from boost/container/detail/pair.hpp to the headers where the primary templates are defined:  
  
  - is_trivially_destructible        (type_traits.hpp)  
  - is_trivially_copy_constructible  (type_traits.hpp)  
  - is_trivially_move_constructible  (type_traits.hpp)  
  - is_trivially_copy_assignable     (type_traits.hpp)  
  - is_trivially_move_assignable     (type_traits.hpp)  
  - has_trivial_destructor_after_move (traits.hpp)  
  
When the specializations live in pair.hpp (included late via flat_map.hpp), GCC diagnoses "partial specialization of '...' after instantiation of '...'" in unity/jumbo builds or any translation unit that implicitly instantiates a trait for std::pair before pair.hpp is included.  This is a regression introduced in Boost 1.88/1.90 by commit 9552828 and is the same class of bug as Trac #12534.  
  
The new specializations implement direct member-wise checks (trait<A>::value && trait<B>::value) instead of inheriting from the dtl::pair specializations, so they are self-contained within Boost.Move.  
  
Fixes: https://github.com/boostorg/container/issues/330

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2026-02-16 21:40:35 UTC  
> Url: https://github.com/boostorg/move/pull/64#issuecomment-3910624876  

Thanks for the pull request and the problem analysis. Other specializations (like "is_union", "is_class", etc.) are also needed. I'll use a slightly different solution, inspired by your pull request.

---
