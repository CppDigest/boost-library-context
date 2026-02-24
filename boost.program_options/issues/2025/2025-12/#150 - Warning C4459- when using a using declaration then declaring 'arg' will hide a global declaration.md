# #150 - [MSVC] Warning C4459: when using a using declaration then declaring 'arg' will hide a global declaration [Closed]

> Username: brandl-muc  
> Created at: 2025-12-04 10:31:32 UTC  
> Updated at: 2025-12-04 11:44:37 UTC  
> Closed at: 2025-12-04 11:07:36 UTC  
> Url: https://github.com/boostorg/program_options/issues/150  

In header `include/boost/program_options/detail/value_semantic.hpp` line 20 (currently) defines a global named `arg`:  
```  
    extern BOOST_PROGRAM_OPTIONS_DECL std::string arg;  
```  
  
This global is defined in the namespace `boost::program_options`, so all should be good. But if client code uses a using directive (`using namespace boost::program_options;`), then the declaration of another `arg` will trigger [warning C4459](https://learn.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-4-c4459?view=msvc-170).  
  
The relevant statement on the referenced page is:  
> One way to fix this issue is to create a namespace for your globals, but not use a using directive to bring that namespace into scope, so all references must use the unambiguous qualified names  
  
This issue could probably be fixed by moving `arg` to namespace `boost::program_options::detail`.  
  
This can be reproduced with the following MRE with Boost 1.89.0 (vcpkg):  
```  
#include <boost/program_options.hpp>  
  
using namespace boost::program_options; // bad  
  
int main()  
{  
    int arg = 0;  
    return arg;  
}  
```  
  
This results in:  
```  
mre.cpp(7,9): error C2220: the following warning is treated as an error  
(compiling source file '/mre.cpp')  
mre.cpp(7,9): warning C4459: declaration of 'arg' hides global declaration  
(compiling source file '/mre.cpp')  
    ...\boost\program_options\detail\value_semantic.hpp(16,51):  
    see declaration of 'boost::program_options::arg'  
Done building project "mre.vcxproj" -- FAILED.  
```  
  
The warning is not produced if  
* the using directive is removed  
* the using directive is moved into a function scope  
Especially the second approach is apt to work around this issue on the client side. As an alternative `namespace po = boost::program_options` could be used.   
  
To make matters worse, when combining the above with `boost::lexical_cast()` then a function will be defined with an argument named `arg` which will likewise trigger this warning: `static inline bool try_convert(const Source& arg, Target& result)`

---

## Comment 1

> Username: vprus  
> Created at: 2025-12-04 10:46:59 UTC  
> Url: https://github.com/boostorg/program_options/issues/150#issuecomment-3611469261  

Thanks for the report.  
  
It appears to me that moving that variable into the `detail` sub-namespace would indeed be the best solution.

---

## Comment 2

> Username: brandl-muc  
> Created at: 2025-12-04 10:50:16 UTC  
> Url: https://github.com/boostorg/program_options/issues/150#issuecomment-3611482128  

I would be willing to change this and create a PR of you are ok.  
Anything I would have to consider?

---

## Comment 3

> Username: vprus  
> Created at: 2025-12-04 10:51:39 UTC  
> Url: https://github.com/boostorg/program_options/issues/150#issuecomment-3611488043  

Let's see if Copilot beats us to it, first :-)

---

## Comment 4

> Username: vprus  
> Created at: 2025-12-04 11:08:17 UTC  
> Url: https://github.com/boostorg/program_options/issues/150#issuecomment-3611583853  

Done, thanks again for raising the issue.

---

## Comment 5

> Username: brandl-muc  
> Created at: 2025-12-04 11:44:37 UTC  
> Url: https://github.com/boostorg/program_options/issues/150#issuecomment-3611776278  

Wow, not a complicated change, still impressive.  
Thank you for the library.
