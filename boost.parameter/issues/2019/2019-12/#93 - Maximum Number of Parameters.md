# #93 - Maximum Number of Parameters [Closed]

> Username: ambmax00  
> Created at: 2019-12-11 10:33:57 UTC  
> Updated at: 2019-12-11 11:51:52 UTC  
> Closed at: 2019-12-11 11:51:51 UTC  
> Url: https://github.com/boostorg/parameter/issues/93  

Hi,  
Is there a maximum number of allowed parameters?  
This one fails for me:  
```  
BOOST_PARAMETER_MEMBER_FUNCTION(    
		(void), get_info, tag,    
		(required (NIL,*))  
		(optional (out(nblks_total),*,nullopt)  
				  (out(nfull_total),*,nullopt)  
				  (out(nblks_local),*,nullopt)  
				  (out(nfull_local),*,nullopt)  
				  (out(pdims),*,nullopt)  
				  (out(my_ploc),*,nullopt)  
				  (out(blks_local),*,nullopt)  
				  (out(proc_dist),*,nullopt)  
				  (out(blk_size),*,nullopt)  
			          (out(blk_offset),*,nullopt)  
			          (out(name),*,nullopt)  
	)  
 ){ ... }  
```  
With:  
```  
error: wrong number of template arguments (11, should be at least 1)  
  BOOST_PARAMETER_MEMBER_FUNCTION(  
```  
  
But it's okay if I remove a few of them.

---

## Comment 1

> Username: ambmax00  
> Created at: 2019-12-11 11:51:51 UTC  
> Url: https://github.com/boostorg/parameter/issues/93#issuecomment-564506476  

Never mind! I just found that defining BOOST_PARAMETER_MAX_ARITY to a higher number than 8 solves it.
