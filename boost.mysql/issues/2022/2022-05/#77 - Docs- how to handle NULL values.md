# #77 - Docs: how to handle NULL values [Closed]

> Username: anarthal  
> Created at: 2022-05-14 14:36:01 UTC  
> Updated at: 2022-12-16 11:59:22 UTC  
> Closed at: 2022-12-16 11:59:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/77  

[This section](https://anarthal.github.io/mysql/mysql/values.html) is missing a paragraph on how to handle NULL values effectively. Provide an example showing something like:  
```  
value v = /* get the value */  
if (v.is_null()) {  
    // handle the NULL case  
} else {  
    double d = v.get<double>(); // will throw on type mismatch  
}  
```
