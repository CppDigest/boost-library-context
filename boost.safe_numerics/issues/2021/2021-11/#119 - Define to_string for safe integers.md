# #119 - Define to_string for safe integers [Open]

> Username: Eelis  
> Created at: 2021-11-29 12:51:26 UTC  
> Updated at: 2021-11-29 12:51:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/119  

Because it would be convenient if code like the following worked generically for both regular ints and safe ints:  
  
    template<typename I>  
    std::string f(I i)  
    {  
        using std::to_string;  
        return to_string(i);  
    }
