# #50 - Issue for BOOST_PP_REMOVE_PARENS macro [Open]

> Username: akeyliu  
> Created at: 2023-01-17 02:40:13 UTC  
> Updated at: 2023-04-10 09:54:25 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/50  

I'm now using BOOST preprocess library for generate set/get/to_json/from_json code automatically. But I find some problem when using BOOST_PP_REMOVE_PARENS macro. Please check it.  
Follow is the code in remove_parens.hpp with line 24-36.    
The code for line 31  which should be (param) not (param)() in the original code.  
  
#define BOOST_PP_REMOVE_PARENS(param) \  
    BOOST_PP_IIF \  
      ( \  
      BOOST_PP_IS_BEGIN_PARENS(param), \  
      BOOST_PP_REMOVE_PARENS_DO, \  
      BOOST_PP_IDENTITY \  
      ) \  
    (param)() \                                            /// The content should be (param) not (param)().  
/**/  
  
#define BOOST_PP_REMOVE_PARENS_DO(param) \  
  BOOST_PP_IDENTITY(BOOST_PP_TUPLE_ENUM(param)) \  
/**/

---

## Comment 1

> Username: akeyliu  
> Created at: 2023-04-10 09:54:25 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/50#issuecomment-1501623273  

I found the bug is that:  
  
In the file: include\boost\preprocessor\punctuation\remove_parens.hpp  
Line 34-35:  
#define BOOST_PP_REMOVE_PARENS_DO(param) \  
  BOOST_PP_IDENTITY(BOOST_PP_TUPLE_ENUM(param)) \  
  
This will fail if the param is not TUPLE but SEQ or LIST.   
  
I found the error when I am using SEQ and correct after I change to TUPLE.  
  
Hope this can help!
