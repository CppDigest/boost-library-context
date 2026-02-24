# #399 - vector_tag in boost.mpl has different implementations [Closed]

> Username: xiangfan-ms  
> Created at: 2018-05-29 20:37:05 UTC  
> Updated at: 2018-06-04 18:34:56 UTC  
> Closed at: 2018-06-04 18:34:56 UTC  
> Url: https://github.com/boostorg/hana/issues/399  

Hi Louis,  
  
I'm Xiang from the Visual C++ compiler team. This issue is found when I am trying to build boost.hana with MSVC.  
  
boost::mpl::aux::vector_tag depends on BOOST_MPL_CFG_TYPEOF_BASED_SEQUENCES  
It is a class if the macro is defined.  
It is a class template if the macro is not defined  
  
The macro is only defined for GCC (which has typeof):  
  
#if !defined(BOOST_MPL_CFG_TYPEOF_BASED_SEQUENCES) \  
    && !defined(BOOST_MPL_PREPROCESSING_MODE) \  
    && defined(BOOST_MPL_CFG_HAS_TYPEOF)  
  
#define BOOST_MPL_CFG_TYPEOF_BASED_SEQUENCES  
  
#endif  
  
The logic in hana\ext\boost\mpl\vector.hpp only handles the class case.  
  
I added the logic to handle the class template case in the repo https://github.com/xiangfan-ms/hana. See the logic under the macro 'BOOST_HANA_WORKAROUND_MSVC_MPL_NO_TYPEOF'.

---

## Comment 1

> Username: ldionne  
> Created at: 2018-05-30 03:36:44 UTC  
> Url: https://github.com/boostorg/hana/issues/399#issuecomment-393019923  

It is wicked that MPL does that. Thanks for the report -- I'll fix this.
