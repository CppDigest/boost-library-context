# #48 - Make the semantics of evaluate() more explicit, if needed [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 21:33:29 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-25 22:12:21 UTC  
> Url: https://github.com/boostorg/yap/issues/48  

Evaluating expressions: Given the direction of the discussion (i.e., dropping customization points), it seems that this will be reduced to "Boost.YAP expressions are explicitly evaluated by calling the evaluate()".  It seems, however, that this could be a good point to include information on (i) evaluation of operators follows native semantics for the types involved, (ii) the value of local reasoning about semantics, (iii) a brief discussion of equivalencies and differences between the underlying native code and what is evaluated by evaluate().  
  
I'll make this more explicit.
