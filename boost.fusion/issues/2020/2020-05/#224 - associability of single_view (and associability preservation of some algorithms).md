# #224 - associability of single_view (and associability preservation of some algorithms) [Open]

> Username: jhcarl0814  
> Created at: 2020-05-29 11:07:25 UTC  
> Updated at: 2020-05-29 11:07:25 UTC  
> Url: https://github.com/boostorg/fusion/issues/224  

if input of these algorithms models AssociativeSequence, then output will model AssociativeSequence, because:  
```  
erase and erase_key use: iterator_range, joint_view  
pop_back and pop_front use: iterator_range  
```  
```  
iterator_range and joint_view preserve associability  
```  
  
if input of these algorithms models AssociativeSequence, then output will ***not*** model AssociativeSequence, because:  
```  
push_back and push_front use: single_view, joint_view  
insert and insert_range use: iterator_range, single_view, joint_view  
```  
```  
single_view does not model AssociativeSequence  
```  
  
### can fusion let single_view model AssociativeSequence?
