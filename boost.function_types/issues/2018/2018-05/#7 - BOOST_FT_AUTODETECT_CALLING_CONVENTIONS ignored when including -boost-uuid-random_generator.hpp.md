# #7 - BOOST_FT_AUTODETECT_CALLING_CONVENTIONS ignored when including <boost/uuid/random_generator.hpp> [Open]

> Username: tobias-loew  
> Created at: 2018-05-17 10:39:31 UTC  
> Updated at: 2022-02-04 20:19:52 UTC  
> Url: https://github.com/boostorg/function_types/issues/7  

Hi,  
  
I' ve got a project which uses Boost.UUID and function_types.  
When compiling for Windows-x86 I define BOOST_FT_AUTODETECT_CALLING_CONVENTIONS before including  
<boost/uuid/random_generator.hpp>   
thereafter I include  
<boost/function_types/config/config.hpp>  
  
but then "boost::function_types::stdcall_cc" is not defined.  
There seems to be a problem with the define/undef of the macro "callable_builtin" in <boost/function_types/detail/encoding/aliases_[un]def.hpp>  
  
When I first include <boost/function_types/config/config.hpp>the type "boost::function_types::stdcall_cc" is defined.  
I'm not sure if the define/undef of the macro "callable_builtin" could even violate ODR.

---

## Comment 1

> Username: Abhibalu123  
> Created at: 2020-11-27 16:13:25 UTC  
> Url: https://github.com/boostorg/function_types/issues/7#issuecomment-734902435  

Hi! I would like to work on this issue can you suggest me ways to get started?

---

## Comment 2

> Username: tobias-loew  
> Created at: 2020-11-28 13:28:52 UTC  
> Url: https://github.com/boostorg/function_types/issues/7#issuecomment-735231308  

@Abhibalu123: The following code shows the problem: when compiled with Visual Studio 2019 (or 2017, 2015), Win32-x86  and only "CASE_WITH_UUID_FAILS" defined then `boost::function_types::stdcall_cc` is not defined.  
Please let me know, if you need more information.   
Thanks!  
  
```  
  
//#define CASE_WITHOUT_UUID_OK  
#define CASE_WITH_UUID_FAILS  
//#define CASE_WITH_FUNCTION_CONFIG_AND_UUID_OK  
  
  
#define BOOST_FT_AUTODETECT_CALLING_CONVENTIONS  
  
#include <boost/config.hpp>  
  
#ifdef CASE_WITHOUT_UUID_OK  
#endif  
  
  
#ifdef CASE_WITH_UUID_FAILS  
#include <boost/uuid/random_generator.hpp>  
#endif  
  
  
#ifdef CASE_WITH_FUNCTION_CONFIG_AND_UUID_OK  
#include <boost/function_types/config/config.hpp>  
#include <boost/uuid/random_generator.hpp>  
#endif  
  
  
#include <boost/function_types/function_type.hpp>  
  
int main()  
{  
    using cc = boost::function_types::stdcall_cc;  
}  
  
```

---

## Comment 3

> Username: jeking3  
> Created at: 2022-02-04 20:19:52 UTC  
> Url: https://github.com/boostorg/function_types/issues/7#issuecomment-1030320448  

Perhaps the property_tags.hpp header in this repository does not include config when it should?  
  
see: https://github.com/boostorg/uuid/issues/114#issuecomment-644102699
