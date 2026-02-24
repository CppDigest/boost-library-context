# #6 - Construction and destruction? [Open]

> Username: denzor200  
> Created at: 2021-07-19 19:31:19 UTC  
> Updated at: 2021-07-19 19:31:19 UTC  
> Url: https://github.com/boostorg/lambda2/issues/6  

I propose to do it like in a boost.lambda. Additionally adding `aggregate` and `new_aggregated_ptr`:  
```  
constructor<T>()(arg_list)                      T(arg_list)  
aggregate<T>()(arg_list)                        T{arg_list}  
destructor()(a)                                 a.~A(), where a is of type A  
destructor()(pa)                                pa->~A(), where pa is of type A*  
new_ptr<T>()(arg_list)                          new T(arg_list)  
new_aggregated_ptr<T>()(arg_list)               new T{arg_list}  
new_array<T>()(sz, val_list)                    new T[sz]{val_list}  
delete_ptr()(p)                                 delete p  
delete_array()(p)                               delete[] p  
```  
https://www.boost.org/doc/libs/1_76_0/doc/html/lambda/le_in_details.html#lambda.construction_and_destruction
