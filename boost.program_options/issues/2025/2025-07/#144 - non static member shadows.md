# #144 - non static member shadows [Open]

> Username: bmagistro  
> Created at: 2025-07-29 13:58:16 UTC  
> Updated at: 2025-07-29 13:58:16 UTC  
> Url: https://github.com/boostorg/program_options/issues/144  

When compiling with clang 19.1.7 on Alma with -Werror and a number of other flags (we run with most warnings enabled), the line https://github.com/boostorg/program_options/blob/develop/include/boost/program_options/parsers.hpp#L162 was flagged with `error: non-static data memeber 'm_desc' of 'basic_command_line_parser' shadows member inherited from type 'cmdline' [-Werror, -Wshadow-field]`.   We opted to add a local patch to suppress this using pragma push/pop at the above location.  Someone with more familiarity with the code may have a more appropriate solution.  If the push/pop is the desired solution, happy to submit a PR with that change included.  
  
Env:  
Alma 9.6  
Boost 1.88.0 via cmake  
clang 19.1.7
