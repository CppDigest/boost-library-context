# #125 - What is the intent of BOOST_FIT_STATIC_LAMBDA [Closed]

> Username: viboes  
> Created at: 2016-03-06 00:50:36 UTC  
> Updated at: 2016-03-06 23:58:20 UTC  
> Closed at: 2016-03-06 01:08:07 UTC  
> Url: https://github.com/boostorg/hof/issues/125  

The implementation defines it as  
  
```  
#define BOOST_FIT_STATIC_LAMBDA BOOST_FIT_DETAIL_MAKE_STATIC = []  
```  
  
Why do we need a macro?

---

## Comment 1

> Username: viboes  
> Created at: 2016-03-06 01:08:07 UTC  
> Url: https://github.com/boostorg/hof/issues/125#issuecomment-192775779  

Forget the question, I read it as it was   
  
```  
#define BOOST_FIT_STATIC_LAMBDA_BOOST_FIT_DETAIL_MAKE_STATIC = []  
```
