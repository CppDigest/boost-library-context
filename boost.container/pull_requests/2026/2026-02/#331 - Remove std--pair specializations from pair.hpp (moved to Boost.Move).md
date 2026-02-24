# #331 Remove std::pair specializations from pair.hpp (moved to Boost.Move) [Closed]

> Username: janzizazizka  
> Created at: 2026-02-09 09:57:52 UTC  
> Updated at: 2026-02-16 21:55:43 UTC  
> Closed at: 2026-02-16 21:55:43 UTC  
> Url: https://github.com/boostorg/container/pull/331  

Remove the std::pair<A,B> partial specializations for is_trivially_{destructible,copy_constructible,move_constructible, copy_assignable,move_assignable} and has_trivial_destructor_after_move from boost/container/detail/pair.hpp.  
  
These specializations are now provided directly in the Boost.Move headers (type_traits.hpp and traits.hpp) alongside the primary template definitions.  Placing them in pair.hpp — which is included late in the include chain via flat_map.hpp — caused GCC to diagnose "partial specialization after instantiation" in unity/jumbo builds or any translation unit that implicitly instantiated a trait for std::pair before pair.hpp was included.  
  
The dtl::pair specializations remain in pair.hpp since dtl::pair is defined in this header.  
  
This is a coordinated fix with the corresponding Boost.Move change.  
  
Fixes: https://github.com/boostorg/container/issues/330

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2026-02-16 21:55:43 UTC  
> Url: https://github.com/boostorg/container/pull/331#issuecomment-3910663286  

I've applied a different patch, but your patch was helpful diagnosing and proposing the fix, including the single tu testcase. Thanks!

---
