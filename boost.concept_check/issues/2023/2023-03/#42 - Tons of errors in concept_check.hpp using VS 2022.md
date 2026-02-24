# #42 - Tons of errors in concept_check.hpp using VS 2022 [Open]

> Username: Sil3ntStorm  
> Created at: 2023-03-03 15:53:25 UTC  
> Updated at: 2023-03-16 09:54:20 UTC  
> Url: https://github.com/boostorg/concept_check/issues/42  

There are about a million (slight exaggeration) errors in concept_check.hpp, such as:  
  
```  
identifier "BOOST_PP_IIF_BOOST_PP_BOOL_" is undefined concept_check.hpp:70  
type name is not allowed concept_check.hpp:80  
too few arguments for class template "boost::Integer" concept_check.hpp:80  
```  
These are repeated for basically every usage of the BOOST_concept macro in concept_check.hpp. The line number obviously changes.

---

## Comment 1

> Username: ZeroXeroZyro  
> Created at: 2023-03-03 22:18:51 UTC  
> Url: https://github.com/boostorg/concept_check/issues/42#issuecomment-1454202666  

> There are about a million (slight exaggeration) errors in concept_check.hpp, such as:  
>   
> ```  
> identifier "BOOST_PP_IIF_BOOST_PP_BOOL_" is undefined concept_check.hpp:70  
> type name is not allowed concept_check.hpp:80  
> too few arguments for class template "boost::Integer" concept_check.hpp:80  
> ```  
>   
> These are repeated for basically every usage of the BOOST_concept macro in concept_check.hpp. The line number obviously changes.  
  
I was able to solve this problem by changing one of the C/C++ Preprocessor properties for my project. Under the C/C++ dropdown, select Preprocessor and go to the "Use Standard Conforming Preprocessor", select "Yes (/Zc:preprocessor)". All errors I encountered with the concept_check macros were resolved after applying.

---

## Comment 2

> Username: PCzachoA  
> Created at: 2023-03-16 09:54:19 UTC  
> Url: https://github.com/boostorg/concept_check/issues/42#issuecomment-1471634963  

Thank you, it wok for me as well. Problem solved.
