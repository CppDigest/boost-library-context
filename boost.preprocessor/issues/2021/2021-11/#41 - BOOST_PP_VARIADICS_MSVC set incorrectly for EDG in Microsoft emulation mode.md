# #41 - BOOST_PP_VARIADICS_MSVC set incorrectly for EDG in Microsoft emulation mode [Open]

> Username: wmm-at-edg  
> Created at: 2021-11-01 21:11:04 UTC  
> Updated at: 2023-12-08 12:01:13 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/41  

In preprocessor/config/config.hpp, the condition for setting BOOST_PP_VARIADICS_MSVC to 1 is:  
  
`# if defined _MSC_VER && _MSC_VER >= 1400 && !defined(__clang__) && (defined(__INTELLISENSE__) || (defined(__INTEL_COMPILER) && __INTEL_COMPILER >= 1700) || !(defined __EDG__ || defined __GCCXML__ || (defined __NVCC__ && defined __CUDACC__) || defined __PATHSCALE__ || defined __DMC__ || defined __CODEGEARC__ || defined __BORLANDC__ || defined __MWERKS__ || defined __SUNPRO_CC || defined __HP_aCC || defined __MRC__ || defined __SC__ || defined __IBMCPP__ || defined __PGI)) && (!defined(_MSVC_TRADITIONAL) || _MSVC_TRADITIONAL)  
`  
Unless modified by a customer, when the EDG front end is invoked in Microsoft emulation mode, it defines both _MSC_VER and __EDG__, with the value of _MSVC_TRADITIONAL being set depending on the --[no_]_ms_std_preprocessor command-line option. When the EDG front end is invoked emulating the traditional Microsoft preprocessor (currently the default in Microsoft emulation mode), this results in defining BOOST_PP_VARIADICS_MSVC to 0, causing errors when Boost preprocessor variadics are used.  
  
Presumably the fix is simply to remove the "defined __EDG__" test in this conditional.

---

## Comment 1

> Username: richarno  
> Created at: 2023-12-08 12:01:12 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/41#issuecomment-1847057214  

This one is annoying. Could someont integrate the fix, which is available above?  
Thanks.
