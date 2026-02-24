# #2 Initialize data_ in value_base default constructor [Closed]

> Username: tempoz  
> Created at: 2014-09-02 16:56:14 UTC  
> Updated at: 2014-10-28 08:43:10 UTC  
> Closed at: 2014-10-28 08:43:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/2  

This means data_ should get initialized in the default constructor for boost::unordered::detail::unique_node (and any other inheritors), as this constructor will be called there.  
  
This uninitialized data member was reported by Coverity (CID 49445), which unfortunately does not seem to have any convenient way to publicly, globally address issues.

---

## Comment 1

> Username: danieljames  
> Created_at: 2014-10-28 08:43:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/2#issuecomment-60724360  

Cherry-picked into develop here: 8c5aa5086d77d62ecf80ea10e0c684335435d6e7  
Merged into master here: bf0f90ff034b0f7d35292ee2c8ee1d8e7d8ecd4a

---
