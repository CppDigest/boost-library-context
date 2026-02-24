# #35 - Refactor eval_if_else() only to evaluate the true branch [Closed]

> Username: tzlaine  
> Created at: 2018-02-14 21:40:39 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-20 07:04:23 UTC  
> Url: https://github.com/boostorg/yap/issues/35  

From Steven Watanabe:  
detail/default_eval.hpp:  
192,332: return eval_if_else(  
   It looks like this always evaluates both branches.
