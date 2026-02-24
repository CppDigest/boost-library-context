# #12 fix function_ref Callable constructor [Merged]

> Username: cmazakas  
> Created at: 2024-06-20 18:09:18 UTC  
> Updated at: 2024-06-21 14:46:52 UTC  
> Merged at: 2024-06-21 02:30:24 UTC  
> Closed at: 2024-06-21 02:30:24 UTC  
> Url: https://github.com/boostorg/compat/pull/12  

We weren't properly exercising our handling of const references which results in a compilation failure when creating a `function_ref` out of a const reference to a function object.  
  
We still preserve the const-ness via NTTPs and properly apply it when needed during invocation.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-06-20 18:12:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/12#pullrequestreview-2131073682  

📁 test/function_ref_obj_noexcept_test.cpp

```diff
 147 |+       BOOST_TEST_EQ(fv2(1), 1);
 148 |+ 
 149 |+       auto& cfref = f1;
```

> Username: pdimov  
> Created_at: 2024-06-20 18:12:18 UTC  
> Url: https://github.com/boostorg/compat/pull/12#discussion_r1647963581  

Shouldn't this be `auto const&`?

> Username: cmazakas  
> Created_at: 2024-06-20 18:31:22 UTC  
> Url: https://github.com/boostorg/compat/pull/12#discussion_r1647982468  

Addressed with the fixup here:  
https://github.com/boostorg/compat/compare/a0bcaa49d267be2f2f2d9eeee5ed143510111c6a..2a5e8031f8b6241efe83af0c634419ac16945c9e  
  
Good catch!


---
