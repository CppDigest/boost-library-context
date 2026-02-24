# #164 - is_reflectable documentation [Open]

> Username: anarthal  
> Created at: 2024-03-21 16:04:19 UTC  
> Updated at: 2024-10-17 09:09:36 UTC  
> Url: https://github.com/boostorg/pfr/issues/164  

I've been trying to understand `is_reflectable`, but I fail to get what to pass as second argument. My case looks like this:  
  
```  
template <class RowType>  
class static_results {  
    static_assert(pfr::is_pfr_reflectable<RowType, Something>::value, "RowType must be reflectable");  
    // Implementation  
};  
```  
  
I want to check that my user type provided me with a `RowType` that is a structure that can be reflected, i.e. I can call `structure_to_tuple`, `for_each_field` and friends on it.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-03-21 23:04:20 UTC  
> Url: https://github.com/boostorg/pfr/issues/164#issuecomment-2014003802  

I thought I had it with this line:  
  
```  
static_assert(pfr::is_implicitly_reflectable<MyType, struct t>(), "");  
```  
  
But it looks like it only returns a suitable value on C++17 and above. Otherwise it returns always `false`. From reading the code, it looks like implicit reflection is referring to the structured bindings reflection technique available only on C++17. However, this is never stated in the docs, and the "always return false" behavior is surprising.

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-05-21 08:38:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/164#issuecomment-2122082247  

I'd appreciate a PR with the fixes :)
