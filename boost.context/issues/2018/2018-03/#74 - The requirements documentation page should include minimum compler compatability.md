# #74 - The requirements documentation page should include minimum compler compatability [Closed]

> Username: teeks99  
> Created at: 2018-03-11 12:35:59 UTC  
> Updated at: 2018-03-11 14:46:07 UTC  
> Closed at: 2018-03-11 14:46:06 UTC  
> Url: https://github.com/boostorg/context/issues/74  

We need to include minimum compiler versions (and switches?) supported for this library on the "requirements" page of the documentation. It is noted that a C++11 compiler is required, but msvc-12.0 (Visual Studio 2013) doesn't work....even though microsoft proudly trumpeted it as C++11 compliant (with a couple minor provisos). GCC/Clang version required would also be nice.

---

## Comment 1

> Username: olk  
> Created at: 2018-03-11 14:46:06 UTC  
> Url: https://github.com/boostorg/context/issues/74#issuecomment-372120794  

The unit-tests are protexted by some feature tests:  
cxx11_auto_declarations  
cxx11_constexpr  
cxx11_defaulted_functions  
cxx11_final  
cxx11_hdr_thread  
cxx11_hdr_tuple  
cxx11_lambdas  
cxx11_noexcept  
cxx11_nullptr  
cxx11_rvalue_references  
cxx11_template_aliases  
cxx11_thread_local  
cxx11_variadic_templates  
  
Your host  teeks99-08-e-win2012R2-64on64 already successfully checks that MSVC-12.0 is not compliant.
