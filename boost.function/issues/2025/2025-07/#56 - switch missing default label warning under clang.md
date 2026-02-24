# #56 - switch missing default label warning under clang [Open]

> Username: bmagistro  
> Created at: 2025-07-29 13:18:22 UTC  
> Updated at: 2025-07-29 13:42:45 UTC  
> Url: https://github.com/boostorg/function/issues/56  

When compiling under clang 19.1.7 with werror and a number of other flags (we run with most warnings enabled), the line https://github.com/boostorg/function/blob/develop/include/boost/function/function_base.hpp#L147 is flagged with `error: switch missing default label [-Werror,-Wswitch-default]`.  Given this is based on the enum at https://github.com/boostorg/function/blob/f6b538dbed2e3047dcbd09eb3f5c7f1ffa90d0a9/include/boost/function/function_base.hpp#L105 and all cases are handled a pr has not been provided at this time.  I see two approaches, one is doing a compiler warning suppression and the other is adding the default label with a throw or similar as it should be an unreachable place based on the current code.  The suppression seems like the simpler answer but may cause a future update to the enum to be missed here while the throw would prevent that at the cost of potentially changing behavior if something is flagged nothrow.  Happy to submit a PR but would need guidance no how it should be approached here.  
  
Env:  
Alma 9.6  
Boost 1.88.0 via cmake  
clang 19.1.7
