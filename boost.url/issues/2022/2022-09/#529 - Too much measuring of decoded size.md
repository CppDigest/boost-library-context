# #529 - Too much measuring of decoded size [Closed]

> Username: vinniefalco  
> Created at: 2022-09-10 00:35:16 UTC  
> Updated at: 2022-09-27 01:22:24 UTC  
> Closed at: 2022-09-27 01:22:24 UTC  
> Url: https://github.com/boostorg/url/issues/529  

`apply_query` for example causes construction of `pct_string_view`. We need to revisit things like `query_rule` to see if they can store either the decoded size in the value type, or have something like this  
```  
struct value_type  
{  
    pct_string_view query;  
    std::size_t parameter_count;  
};  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-09-26 22:08:27 UTC  
> Url: https://github.com/boostorg/url/issues/529#issuecomment-1258693874  

All occurences of `apply_query` already receive `pct_string_view`, which already includes the decoded size, and the parameter count from `query_part_rule`.  
  
```cpp  
// [ "?" query ]  
    {  
        auto rv = grammar::parse(  
            it, end, detail::query_part_rule);  
        if(! rv)  
            return rv.error();  
        if(rv->has_query)  
        {  
            // map "?" to { {} }  
            u.apply_query(  
                rv->query,  
                rv->count +  
                    rv->query.empty());  
        }  
    }  
```  
  
Maybe this issue has fixed itself?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-27 00:55:53 UTC  
> Url: https://github.com/boostorg/url/issues/529#issuecomment-1258834109  

> Maybe this issue has fixed itself?  
  
Very possible
