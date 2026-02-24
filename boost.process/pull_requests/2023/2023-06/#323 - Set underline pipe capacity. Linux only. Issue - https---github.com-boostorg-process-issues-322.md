# #323 Set underline pipe capacity. Linux only. Issue - https://github.com/boostorg/process/issues/322 [Closed]

> Username: SilverPlate3  
> Created at: 2023-06-28 20:13:08 UTC  
> Updated at: 2023-07-01 06:11:41 UTC  
> Closed at: 2023-07-01 06:11:41 UTC  
> Url: https://github.com/boostorg/process/pull/323  

Possible Solution to https://github.com/boostorg/process/issues/322.  
Including unit tests.   
  
Please let me know if:  
1) You prefer the API to be part of the bp::opstream/bp::ipstream. So users need to go into ip.pipe.set_pipe_capacity().  
2) Is it ok I included <string> and <fstream>?   I saw you use them only in the unit tests. However because of the #if, I think it won't cause compilation errors.   
3) Would you like me to add it to the doc's?  
4) When could it be pushed in?

---

## Comment 1

> Username: SilverPlate3  
> Created_at: 2023-06-28 20:15:34 UTC  
> Url: https://github.com/boostorg/process/pull/323#issuecomment-1612042691  

Please let me know how can I improve my PR, to better suite the code base.

---
