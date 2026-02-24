# #47 - `visit` can't be used in a `constexpr` context [Closed]

> Username: joaquintides  
> Created at: 2024-11-22 19:58:12 UTC  
> Updated at: 2024-11-23 16:26:27 UTC  
> Closed at: 2024-11-23 16:26:26 UTC  
> Url: https://github.com/boostorg/variant2/issues/47  

As examplified [here](https://godbolt.org/z/arbn94bGf). Among other potential causes, this function lacks the required `constexpr` qualifier:  
  
https://github.com/boostorg/variant2/blob/a4b167b7238781d75df5d6684c9e9d40b26456dc/include/boost/variant2/variant.hpp#L2128-L2131

---

## Comment 1

> Username: pdimov  
> Created at: 2024-11-23 16:26:26 UTC  
> Url: https://github.com/boostorg/variant2/issues/47#issuecomment-2495530393  

Fixed (somewhat) on develop. The binary (and more) case requires C++17 because of constexpr lambdas.
