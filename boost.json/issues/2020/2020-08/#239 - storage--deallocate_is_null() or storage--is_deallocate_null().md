# #239 - storage::deallocate_is_null() or storage::is_deallocate_null()? [Closed]

> Username: vinniefalco  
> Created at: 2020-08-31 21:03:11 UTC  
> Updated at: 2020-09-01 15:05:18 UTC  
> Closed at: 2020-09-01 15:05:18 UTC  
> Url: https://github.com/boostorg/json/issues/239  

This is for bikeshedding the function  
```  
bool storage_ptr::deallocate_is_null() const noexcept;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-31 21:04:31 UTC  
> Url: https://github.com/boostorg/json/issues/239#issuecomment-684039245  

This came up because i wrote  
```  
            assert( ! jv.storage().is_deallocate_null() );  
```  
  
in the docs without thinking, as I assumed that the function name matched the trait name. Thus there is a case to be made that the names should match. We also have `storage::is_counted()` so consistency might be improved with renaming.
