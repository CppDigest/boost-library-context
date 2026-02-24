# #25 - Reduce capacity of polygon_{;45_;90_}set with shrink_to_fit functionality [Open]

> Username: sbigalke  
> Created at: 2018-11-19 08:55:45 UTC  
> Updated at: 2018-11-19 08:55:45 UTC  
> Url: https://github.com/boostorg/polygon/issues/25  

First, I'd like to thank you for providing such an awesome library.  
  
But I think the method shrink_to_fit() is missing in order to reduce the capacity of any set.  
  
In your documentation, it is mentioned under the clear() command that one should  
  
> Use shrink to fit idiom and assign default constructed polygon set to deallocate.  
  
but there is no way of getting the vector to "shrink_to_fit()" because the value() function is declared as const.  
So basically, one has no option to deallocate the memory of a huge polygon_set .  
  
An easy fix would be to add a method as follows:  
  
```  
void shrink_to_fit(){  
    data_.shrink_to_fit();  
}  
```
