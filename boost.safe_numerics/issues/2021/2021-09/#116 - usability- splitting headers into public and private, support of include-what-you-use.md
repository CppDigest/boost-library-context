# #116 - usability: splitting headers into public and private, support of include-what-you-use [Open]

> Username: hgkjshegfskef  
> Created at: 2021-09-12 09:33:47 UTC  
> Updated at: 2021-09-15 22:21:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/116  

As of right now it is not clear, which headers are public and which are private. Public headers are meant for inclusion by users of the library. The only way to approximate, whether the include is public or private is to look at documentation of interesting parts or at the examples -- if it is there, it is probably meant to be included, if it's not, it probably is not meant for inclusion.  
  
The motivating issue is usage of external tooling, such is include-what-you-use program. This program can rather accurately determine, which headers the file is missing, or which can be removed, or which can be replaced by a forward declaration, if configured correctly. All in all, it increases correctness of C++ programs, and is thus useful. A sample output of the tool is as follows:  
  
```  
[9/16] Building CXX object src/CMakeFiles/libcherryblazer.dir/canvas.cc.o  
Warning: include-what-you-use reported diagnostics:  
  
/home/runner/work/cherry_blazer/cherry_blazer/src/canvas.hh should add these lines:  
#include <stdint.h>                                           // for uint8_t  
#include <boost/core/enable_if.hpp>                           // for lazy_en...  
#include <boost/cstdint.hpp>                                  // for uint8_t  
#include <boost/logic/tribool.hpp>                            // for operator!  
#include <boost/safe_numerics/checked_default.hpp>            // for cast  
#include <boost/safe_numerics/checked_result_operations.hpp>  // for operator<  
#include <boost/safe_numerics/safe_base_operations.hpp>       // for operator-  
#include <boost/safe_numerics/safe_common.hpp>                // for base_value  
  
/home/runner/work/cherry_blazer/cherry_blazer/src/canvas.hh should remove these lines:  
- #include <boost/safe_numerics/automatic.hpp>  // lines 8-8  
- #include <boost/safe_numerics/safe_integer.hpp>  // lines 9-9  
- #include <boost/safe_numerics/safe_integer_range.hpp>  // lines 11-11  
  
The full include-list for /home/runner/work/cherry_blazer/cherry_blazer/src/canvas.hh:  
#include <stdint.h>                                           // for uint8_t  
#include <boost/core/enable_if.hpp>                           // for lazy_en...  
#include <boost/cstdint.hpp>                                  // for uint8_t  
#include <boost/logic/tribool.hpp>                            // for operator!  
#include <boost/safe_numerics/checked_default.hpp>            // for cast  
#include <boost/safe_numerics/checked_result_operations.hpp>  // for operator<  
#include <boost/safe_numerics/safe_base_operations.hpp>       // for operator-  
#include <boost/safe_numerics/safe_common.hpp>                // for base_value  
#include <boost/safe_numerics/safe_integer_literal.hpp>       // for base_value  
#include <fstream>                                            // for ostream  
#include <memory>                                             // for unique_ptr  
#include <string>                                             // for string  
#include "color.hh"                                           // for Color  
#include "safe_numerics_typedefs.hh"                          // for safe_ul...  
#include "types.hh"                                           // for u16, u8  
---  
```  
  
Now, the problem here is that I used seemingly public includes -- safe_integer, safe_integer_range, automatic, but the tool doesn't know that they are the ones that should be used, and not checked_default, checked_result_operations, etc. One way to teach the tool is to use a [mapping file](https://github.com/include-what-you-use/include-what-you-use/blob/master/docs/IWYUMappings.md), where user can specify, which symbols/includes are public, and which are private. Sample contents of a mapping file:  
  
```  
[  
    # Replace suggested private GTest headers with public one.  
    {include: ["@<gtest/gtest-.*>", "private", "<gtest/gtest.h>", "public"]},  
    {include: ["@<gtest/gtest_.*>", "private", "<gtest/gtest.h>", "public"]}  
]  
```  
  
What this does is, it stops iwyu from suggesting headers that match these wildcards, rendering them private, and instead is instructed to suggest the-one-and-only gtest.h.  
  
This approach can only go so far. A better approach is for library creators to include [pragmas](https://github.com/include-what-you-use/include-what-you-use/blob/master/docs/IWYUPragmas.md) to specify, which headers export which symbols, or simply which headers are part of public interface, and which are for the library implementer.  
  
To summarize, the problem with safe_numerics for the end user who wants to check his program for correct header inclusion is: the library does not have clear separation of public and private headers.  
* Possible solution 1: create a directory called "private" or "detail" and put private headers inside, which will allow the end user to specify a wildcard in iwyu mapping file to mark them private.  
* Possible solution 2: library author works through the headers and marks them with appropriate iwyu pragmas. Less favorable for the library author, since it might take a lot of time getting it right and test that it correctly behaves with default iwyu settings, but in the long run, this approach will more more correct reports from the tool, and perhaps even library developers could use iwyu themselves to analyze header situation in their project, which will also stop iwyu from flagging safe_numerics dependencies, such as tribool in the sample output above.

---

## Comment 1

> Username: robertramey  
> Created at: 2021-09-12 21:06:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/116#issuecomment-917710377  

Hmmm - the documentation on each "public" type includes the header file which should be included.  Each header file includes all the "private" header files it needs.  Does this not work?  There might be some private includes missing, but that should be a trivial fix.

---

## Comment 2

> Username: hgkjshegfskef  
> Created at: 2021-09-13 08:28:59 UTC  
> Updated at: 2021-09-13 08:29:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/116#issuecomment-917961594  

Right, but as you can see from the sample output of the tool, the tool thinks that public headers are useless and suggests to remove them. Instead, it suggests to add private headers. Which makes sense, as the tool is supposed to suggest minimal optimal amount of included symbols, and it doesn't know which includes are public and which are private, so perhaps it traces each symbol to the include it comes from, and perhaps it just so happens that all the needed symbols are first defined in private headers.

---

## Comment 3

> Username: robertramey  
> Created at: 2021-09-15 22:21:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/116#issuecomment-920427016  

looking at this again.  This sounds like the whole C++ Visibility thing to minimize the number of symbols exported at link time. You can see this for the serialization library as well as others.  It diminishes linkage times by a lot.  It's a pain to set up though because different compilers/linkers have slightly different conventions for exporting C++ symbols.
