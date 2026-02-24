# #34 - Fix eval_comma() [Closed]

> Username: tzlaine  
> Created at: 2018-02-14 21:39:24 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-20 07:04:23 UTC  
> Url: https://github.com/boostorg/yap/issues/34  

From Steven Watanabe:  
detail/default_eval.hpp:  
162,310: return eval_comma(  
   Please tell me that I'm reading this wrong and that  
   (a) this can safely return void, and (b) the left  
   argument is evaluated before the right argument.
