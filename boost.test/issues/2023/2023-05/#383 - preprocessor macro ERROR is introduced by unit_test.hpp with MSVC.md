# #383 - preprocessor macro ERROR is introduced by unit_test.hpp with MSVC [Open]

> Username: 98B  
> Created at: 2023-05-30 19:41:24 UTC  
> Updated at: 2023-05-30 20:08:56 UTC  
> Url: https://github.com/boostorg/test/issues/383  

Trying to use Boost.Test (1.81.0) for testing some other library (specifics below),  
using the header-only variant.  With MSVC, either 14.16 or 19.29,  
compilation of the test unit fails.  
(Syntax errors, enum class errors, ... in the library)  
After doing some digging, it seems as if somewhere, somehow, `#include` `unit_test.hpp`  
has the effect of defining macro `ERROR` (MSVC only).  
  
Short reproducing program:  
  
```  
#define BOOST_TEST_MODULE Preprocessor Definitions  
#include <boost/test/included/unit_test.hpp>  
#ifdef ERROR  
#error Uhm  
#endif  
```  
  
Compiling this program using MSVC results in a "fatal error",  
  
```console  
[cmd] cl /P /EHsc /I "%BOOST_ROOT%" uhm.cpp  
  
uhm.cpp  
uhm.cpp(4): fatal error C1189: #error:  Uhm  
```  
  
OK.   
  
The `/P` option results in a preprocessed file (around 7M). Compiling that,  
I get a working program. It also displays the expected result.  
  
```console  
[cmd] cl /EHsc /Tp uhm.i  
```  
  
```  
[cmd] uhm.exe  
Test setup error: test tree is empty  
  
```  
  
As expected, but this is basically ignoring `ERROR`.  
The "real" test case doesn't. `ERROR == 0`.  
  
VS resolves `ERROR` to be from *wingdi.h*, directly below `/* Region Flags */`.  
  
So, does this result confirm that the definition of macro `ERROR` is  
a consequence of including unit_test.hpp when using MSVC?  
  
### Background (ANTLR4)  
  
I `#include unit_test.hpp` as above and then the library's header files.  
The compiler, MSVC around 14.16, bails, sort of.  
Adding `#define NOMINMAX` before unit_test.hpp had changed the diagnostics a little  
(about std::max),  
but only to give way to another long list,  
starting with C2059 (and 3805) about an enum class constant named "ERROR".  
This name is in fact used as an identifier in an enum class of the library.  
  
[Microsoft Docs about C2059](https://learn.microsoft.com/en-us/cpp/error-messages/compiler-errors-1/compiler-error-c2059?view=msvc-170)  
 do hint at an existing preprocessor definition, also showing an enum class as an example.  
  
As a workaround, I can include ANTLR4 headers first, before unit_test.hpp,  
but since this specific test program is also displaying memory leaks,  
I'd rather know I'm following the rules.  
And whether there actually is a bogus `ERROR` introduced somewhere in an MSVC setup.  
Or if I have overlooked something.  
(`/permissive-`, while helpful in general, does not seem to be a complete solution.)  
  
Clang 12 and GCC 8.5 are fine with the programs, on macOS and GNU/Linux.

---

## Comment 1

> Username: 98B  
> Created at: 2023-05-30 20:08:56 UTC  
> Url: https://github.com/boostorg/test/issues/383#issuecomment-1569023316  

`#define NOGDI` is helpful
