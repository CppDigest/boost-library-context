# #138 - bracket_and_solve_root, toms748_solve; underflow when max_iter == 0 [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 17:48:33 UTC  
> Updated at: 2018-08-19 17:57:17 UTC  
> Closed at: 2018-08-19 17:57:17 UTC  
> Url: https://github.com/boostorg/math/issues/138  

~line 295, math/tools/toms748_solve.hpp  
  
std::pair<T, T> toms748_solve(...)  
  
Parameter max_iter is boost::uintmax_t. Line ~343 decrements count (--count) with no prior checks on the value of max_iter, resulting in an underflow if the provided max_iter==0.  
  
This was discovered when calling bracket_and_solve root (with max_iters==15), which internally calls toms748_solve with max_iter==0, leading to the underflow/infinite(?) loop.  
  
Unfortunately due to the nature of bracket_and_solve root I'm unable to provide a minimal repro, but to repro the underflow simply call toms748_solve with max_iter==0, which it does not appear to be checking against.  
  
Alternatively/additionally, bracket_and_solve root is calling toms748_solve with max_iter==0; I don't know if that is desired behavior.  
  
Moved from : https://svn.boost.org/trac10/ticket/13129
