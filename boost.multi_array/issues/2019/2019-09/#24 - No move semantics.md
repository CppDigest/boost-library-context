# #24 - No move semantics [Open]

> Username: allopislozano  
> Created at: 2019-09-12 10:11:28 UTC  
> Updated at: 2019-09-12 10:11:28 UTC  
> Url: https://github.com/boostorg/multi_array/issues/24  

Hello!  
  
I think it would be great if multi_array had move semantics, at the moment unfortunately it is inefficient to return multi_arrays by value and the workaround is not great:  
  
https://stackoverflow.com/questions/42698451/how-to-stdmove-a-boostmulti-array#comment72520077_42698451  
  
I think having move construction / assignment would be great
