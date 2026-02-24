# #13 BOOST_FUSION_ADAPT_ASSOC_STRUCT* deducing types [Merged]

> Username: daminetreg  
> Created at: 2014-07-29 20:59:11 UTC  
> Updated at: 2014-07-29 22:03:39 UTC  
> Merged at: 2014-07-29 22:03:39 UTC  
> Closed at: 2014-07-29 22:03:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/13  

Continuation of  #9, #10 & #11.  
  
This patch contains the BOOST_FUSION_ADAPT_ASSOC_STRUCT\* macros with support for deducing type by omitting it or providing BOOST_FUSION_ADAPT_AUTO.   
  
This patch also updates the documentation of the modified macros until now.  
  
Missing TODO's for type deduction, which will come soon (i.e. I'm currently working on it) are type deduction for :  
- BOOST_FUSION_ADAPT_ADT  
- BOOST_FUSION_ADAPT_TPL_ADT  
- BOOST_FUSION_ADAPT_ASSOC_ADT  
- BOOST_FUSION_ADAPT_ASSOC_TPL_ADT  
  
All test passes on gcc 4.6, 4.8.1, 4.8.2 and clang 3.3 on linux. I'll provide a more detailed and extensive testing on multiple platform (msvc, intel...) as soon as I'm finished with the ADT adaptation macros.  
  
Thank you for the time used by reviewing my changes :).  
Cheers,

---
