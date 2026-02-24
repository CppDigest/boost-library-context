# #459 - url_base::edit_params should return the pointer [Closed]

> Username: vinniefalco  
> Created at: 2022-08-26 01:54:30 UTC  
> Updated at: 2022-09-02 17:22:01 UTC  
> Closed at: 2022-09-02 17:22:01 UTC  
> Url: https://github.com/boostorg/url/issues/459  

`url_base::edit_params` should return the char pointer to the beginning of the modified range, otherwise `params::insert` and other functions like it have to perform an O(n) operation to return the new iterator.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-02 17:22:01 UTC  
> Url: https://github.com/boostorg/url/issues/459#issuecomment-1235742554  

This is done
