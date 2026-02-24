# #11 - arrow operator for flyweight [Closed]

> Username: malaka712  
> Created at: 2023-02-09 13:16:56 UTC  
> Updated at: 2023-04-18 06:45:14 UTC  
> Closed at: 2023-04-17 16:33:37 UTC  
> Url: https://github.com/boostorg/flyweight/issues/11  

Hi,  
  
I would like to suggest adding the arrow-operator to boost::flyweights:  
  
``` flyweight/flyweight.hpp  
   // in flyweight/flyweight.hpp  
   const key_type&   get_key()const{return core::key(h);} // existing  
   const value_type& get()const{return core::value(h);}   // existing  
   operator const    value_type&()const{return get();}    // existing  
   const value_type* operator->()const{return &get();}    // new  
```  
  
Best Regards

---

## Comment 1

> Username: joaquintides  
> Created at: 2023-04-17 16:33:37 UTC  
> Url: https://github.com/boostorg/flyweight/issues/11#issuecomment-1511710720  

Will be shipping in Boost 1.83.

---

## Comment 2

> Username: malaka712  
> Created at: 2023-04-18 06:45:14 UTC  
> Url: https://github.com/boostorg/flyweight/issues/11#issuecomment-1512527787  

Great, thank you!
