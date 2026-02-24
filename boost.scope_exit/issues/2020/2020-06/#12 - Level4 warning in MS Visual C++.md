# #12 - Level4 warning in MS Visual C++ [Open]

> Username: sasha2048  
> Created at: 2020-06-24 12:33:06 UTC  
> Updated at: 2023-03-01 16:02:09 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/12  

Code:  
```C++  
#include <boost/scope_exit.hpp>  
  
int main() {  
    int a = 0;  
    BOOST_SCOPE_EXIT(&a) {++a;} BOOST_SCOPE_EXIT_END  
    return 0;  
}  
```  
  
When compiling with [`/W4`](https://docs.microsoft.com/en-us/cpp/build/reference/compiler-option-warning-level) in MS Visual Studio 2019, I get:  
> warning C4459: declaration of 'boost_scope_exit_aux_args' hides global declaration  
> …\boost\scope_exit.hpp(261,46): message : see declaration of 'boost_scope_exit_aux_args'  
  
See also:  
  
* [#12704 in Trac](https://svn.boost.org/trac10/ticket/12704).

---

## Comment 1

> Username: vivekkushwaha39  
> Created at: 2023-03-01 16:01:45 UTC  
> Updated at: 2023-03-01 16:02:09 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/12#issuecomment-1450398237  

Hi Team  
Any fix/alternative available for this ?
