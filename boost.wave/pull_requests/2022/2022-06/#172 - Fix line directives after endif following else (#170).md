# #172 Fix line directives after endif following else (#170) [Merged]

> Username: jefftrull  
> Created at: 2022-06-27 03:45:10 UTC  
> Updated at: 2022-06-27 03:45:47 UTC  
> Merged at: 2022-06-27 03:45:47 UTC  
> Closed at: 2022-06-27 03:45:47 UTC  
> Url: https://github.com/boostorg/wave/pull/172  

* Fix line directives after endif when else clause is present  
  
The initial part of a conditional block (the if/ifdef/ifndef) takes  
the "slow" path through the code in cpp_iterator.hpp using the C++  
expression grammar, while else and endif directives take the "fast"  
path. The slow path sets must_emit_line_directive, and it will be  
cleared after either else or endif is processed. Unfortunately when  
an else precedes the endif, must_emit_line_directive is cleared and is  
never set, as only the slow path sets it. The result is proper line  
directives are issued for the else clause, but not for the subsequent  
endif.  
  
This changes adds setting of must_emit_line_directive in the "fast"  
path for endif so line directives are considered after an intervening else.

---
