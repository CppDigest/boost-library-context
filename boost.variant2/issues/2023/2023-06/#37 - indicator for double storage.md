# #37 - indicator for double storage [Closed]

> Username: ad8e  
> Created at: 2023-06-19 04:44:09 UTC  
> Updated at: 2023-06-29 22:19:59 UTC  
> Closed at: 2023-06-29 22:19:59 UTC  
> Url: https://github.com/boostorg/variant2/issues/37  

It would be nice to enable something like this:  
  
````  
using myvariant = boost::variant2<int, myclass>;  
static_assert(myvariant::double_storage_required() == false, "some member is forcing double storage");  
````

---

## Comment 1

> Username: pdimov  
> Created at: 2023-06-29 22:19:59 UTC  
> Url: https://github.com/boostorg/variant2/issues/37#issuecomment-1613873491  

I've added this under the name of `uses_double_storage()` and it will be in the next release.
