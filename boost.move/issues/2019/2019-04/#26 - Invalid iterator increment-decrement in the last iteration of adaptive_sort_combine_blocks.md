# #26 - Invalid iterator increment/decrement in the last iteration of adaptive_sort_combine_blocks [Closed]

> Username: igaztanaga  
> Created at: 2019-04-24 18:57:47 UTC  
> Updated at: 2019-04-24 18:59:22 UTC  
> Closed at: 2019-04-24 18:59:22 UTC  
> Url: https://github.com/boostorg/move/issues/26  

In a left/right merge, combined_first is always increased/decreased the regular size:  
  
combined_first += l_reg_combined  
  
combined_first -= l_reg_combined  
  
This is invalid when the last iteration is not regular. The problem shows up when using stable_vector::iterators, which crash when incremented out of bounds.
