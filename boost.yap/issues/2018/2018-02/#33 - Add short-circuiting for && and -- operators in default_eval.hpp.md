# #33 - Add short-circuiting for && and || operators in default_eval.hpp [Closed]

> Username: tzlaine  
> Created at: 2018-02-14 21:38:12 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-17 06:16:03 UTC  
> Url: https://github.com/boostorg/yap/issues/33  

From Steven Watanabe:  
detail/default_eval.hpp:  
149,302: BOOST_YAP_BINARY_OPERATOR_CASE(logical_or) // ||  
150,303: BOOST_YAP_BINARY_OPERATOR_CASE(logical_and) // &&  
   No short circuit evaluation.

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-02-17 06:16:03 UTC  
> Url: https://github.com/boostorg/yap/issues/33#issuecomment-366420406  

Mooted by removal of customization points.
