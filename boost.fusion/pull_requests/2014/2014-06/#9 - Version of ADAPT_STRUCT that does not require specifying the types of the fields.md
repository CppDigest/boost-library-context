# #9 Version of ADAPT_STRUCT that does not require specifying the types of the fields [Merged]

> Username: daminetreg  
> Created at: 2014-06-14 10:38:26 UTC  
> Updated at: 2014-06-16 10:03:51 UTC  
> Merged at: 2014-06-15 00:42:32 UTC  
> Closed at: 2014-06-15 00:42:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/9  

As a continuation of pull-request : https://github.com/boostorg/fusion/pull/3 I create this new one, now targetting the _fusion_adapters_ branch and no more _master_.  
  
---  
  
Hi @djowel and @K-ballo,  
  
I'm writing you today because I think I got a reviewable ( i.e. hopefully good ) state for the `BOOST_FUSION_ADAPT_STRUCT` and `BOOST_FUSION_ADAPT_STRUCT_TPL` deducing the type without separate macros and providing a backward compatible signature to the existing macro.  
  
This was for me the important basis since our discussion about my first easy/naïve implementation of the feature, now this is quite more flexible and better maintainable, because it includes less repetition of the same kind of macros.  
# What is available now in this patch ?  
  
Simply `BOOST_FUSION_ADAPT_STRUCT` and `BOOST_FUSION_ADAPT_STRUCT_TPL` capable of deducing the types of declared fields, but still allowing the old syntax. The unit test to this and the documentation for these two macros.  
  
``` cpp  
BOOST_FUSION_ADAPT_STRUCT(  
    struct_name,  
    member_name0,  
    member_name1,  
    member_name2,  
    ...  
    )  
  
// When BOOST_PP_VARIADICS is missing or you wish to specify type self :  
BOOST_FUSION_ADAPT_STRUCT(  
    struct_name,  
    (member_type0, member_name0)  
    (member_type1, member_name1)  
    (BOOST_FUSION_ADAPT_AUTO, member_name2)  
    ...  
    )  
  
// It can even be mixed, but I wouldn't document it :   
  
BOOST_FUSION_ADAPT_STRUCT(  
    struct_name,  
    member_name0,  
    member_name1,  
    (member_type2, member_name2)  
    (member_type3, member_name3)  
    (BOOST_FUSION_ADAPT_AUTO, member_name4),  
    member_name5,  
    ...  
    )  
  
  
// Note that the same applies for the _TPL versions of ADAPT_STRUCT.  
```  
  
Example of the generated doc :   
- [BOOST_FUSION_ADAPT_STRUCT](http://www.lecbna.org/~sys_lecbna/boost_fusion_type_deduction_for_adapt_struct_doc/fusion/adapted/adapt_struct.html)  
- [BOOST_FUSION_ADAPT_STRUCT_TPL](http://www.lecbna.org/~sys_lecbna/boost_fusion_type_deduction_for_adapt_struct_doc/fusion/adapted/adapt_tpl_struct.html)  
## Rationale about the new ADAPT_STRUCT signature.  
  
It's possible to use the macro without BOOST_PP_VARIADICS support as it was possible before, which is an important deal to ensure backward compatibility. That's why the complexity of the parameter parsing costs a bit readability.  
  
The type deduction feature is also usable in the "old" syntax via the placeholder BOOST_FUSION_ADAPT_AUTO, which is simply defined to BOOST_PP_EMPTY, which would also allow to completely omit it `(, member_name3)`, but it's not something advisable, as even if it works on GCC/Clang  the BOOST_PP_IS_EMPTY facility used to detect the placeholder is not thought to detect emptyness but a macro expanding to nothing or 0.  
  
To this a new signature was added when BOOST_PP_VARIADICS was available, which offers a pretty light syntax, as all unneeded parens were removed in comparison to the first version of this patch. This allows to simply declare the field to take in the adapted sequence as variadic arguments.  
# What is not in this patch ?  
  
It took a bit longer, as I searched long a solution to provide a clean API, often changing my mind, that's why I'm not delivering you in one batch the full feature.   
  
Now that I have this solid basis, I can go on and add type deduction support to the other macros (listed below), but this is more like a copy-paste-reuse work than anything else, so I hope to provide them really soon.  
- BOOST_FUSION_ADAPT_STRUCT_NAMED  
- BOOST_FUSION_ADAPT_ASSOC_STRUCT  
- BOOST_FUSION_ADAPT_ASSOC_STRUCT_NAMED  
- BOOST_FUSION_ADAPT_ADT  
- BOOST_FUSION_ADAPT_ASSOC_ADT  
- And their TPL counterparts naturally  
- Fix the DEFINE_STRUCT_\* macros to work with the new adapt_base  
# Why am I sending this incomplete ?  
  
It's not all macros as said above, but that's the central ones IMHO, and therefore the sooner this part is reviewed, the faster I'll know if it's the way I should follow. Because it's important to me that my proposal please your philosophy and design principles.   
  
So I beg you if you want to, to criticize this state and tell me what you want different. I love critics. :) I even need them.  
  
As all other macros will simply be calls or glue code to this, I think that it makes sense to already propose you these changes for review. :smile:  
  
Thank you for your support, I hope I'll be in time for the Spirit X3 release. :sweat_smile:

---

## Comment 1

> Username: djowel  
> Created_at: 2014-06-15 00:42:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/9#issuecomment-46103446  

Agustin, Damien, I'm going to merge this PR. Since it is in a separate branch, we can check the code and play arund with it without affecting the main devel branch. Once we have things ironed out, we can merge to devel.

---

## Comment 2

> Username: daminetreg  
> Created_at: 2014-06-16 10:03:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/9#issuecomment-46160845  

Nice :) I'll provide tomorrow evening the fixes for the BOOST_FUSION_DEFINE_STRUCTs macros and work on the missing type-deducing : BOOST_FUSION_ADAPT_STRUCT_NAMED, BOOST_FUSION_ADAPT_ASSOC_STRUCT, BOOST_FUSION_ADAPT_ASSOC_STRUCT_NAMED, BOOST_FUSION_ADAPT_ADT, BOOST_FUSION_ADAPT_ASSOC_ADT.

---
