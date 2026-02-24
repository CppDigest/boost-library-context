# #235 - Container-policy parameter for parse functions [Open]

> Username: psiha  
> Created at: 2025-05-05 18:09:04 UTC  
> Updated at: 2025-05-05 18:09:04 UTC  
> Url: https://github.com/boostorg/parser/issues/235  

Consider adding the option to provide a policy type parameter that specifies which containers the library should use by default (to minimize conversions and/or minimize verbosity - need for the user to specify his own preferences) like  
```  
struct containers  
{  
  template <typename T> using vector = boost::container::small_vector<T,...>;  
  template <typename Key, typename Value> using ordered_map = boost::container::flat_map<....>;  
};  
```
