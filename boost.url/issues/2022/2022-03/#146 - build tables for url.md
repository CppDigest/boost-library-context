# #146 - build tables for url [Closed]

> Username: vinniefalco  
> Created at: 2022-03-21 18:01:19 UTC  
> Updated at: 2022-03-25 16:16:42 UTC  
> Closed at: 2022-03-25 16:16:42 UTC  
> Url: https://github.com/boostorg/url/issues/146  

URL does not build or use the lookup tables. It needs to do both. It might be worth looking into pushing the copied strings logic deeper into the implementation first.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-03-21 18:27:03 UTC  
> Url: https://github.com/boostorg/url/issues/146#issuecomment-1074265184  

@vinniefalco do you have a reference of the pattern you would like for the lookup tables? Something from boost.json, maybe? Or only a vector allocated with `const_string::factory` allocator? Some small vector buffer would also be helpful.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-03-21 18:31:17 UTC  
> Url: https://github.com/boostorg/url/issues/146#issuecomment-1074269178  

yeah the table goes at the end of the allocated storage, in reverse order (low indices last). I think there's already some code for this.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-03-21 18:32:31 UTC  
> Url: https://github.com/boostorg/url/issues/146#issuecomment-1074270190  

http-proto handles the tables correctly, we should model the implementation based on that:   
  
https://github.com/CPPAlliance/http_proto/blob/be4c4935515462119d14830cbba7ed1a3ed615d9/include/boost/http_proto/detail/fields_table.hpp#L30

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-03-21 18:33:22 UTC  
> Url: https://github.com/boostorg/url/issues/146#issuecomment-1074270864  

OK. Great!

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-03-25 16:16:42 UTC  
> Url: https://github.com/boostorg/url/issues/146#issuecomment-1079184122  

Closing this PR as there is not enough user demand at this point.
