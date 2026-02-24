# #24 - std::false_type in safe_base constructor [Closed]

> Username: akrzemi1  
> Created at: 2017-02-21 13:23:24 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-10 22:25:17 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/24  

`safe_base` has a constructor taking `std::false_type` as second argument:  
  
```c++  
constexpr explicit safe_base(const Stored & rhs, std::false_type);  
```  
  
There is no argument taking the corresponding `std::true_type`. It looks like the purpose of this constructor is to initialize the value but skip the validation. Maybe, in order to make the intention more clear to whoever reads the code, invent your own empty class, like `skip_validation`, and use it instead?

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-10 22:25:17 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/24#issuecomment-412222212  

done
