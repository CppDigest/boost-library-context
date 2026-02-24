# #274 - to_string optimization [Closed]

> Username: vinniefalco  
> Created at: 2020-09-05 13:25:46 UTC  
> Updated at: 2020-09-07 18:45:38 UTC  
> Closed at: 2020-09-07 18:45:38 UTC  
> Url: https://github.com/boostorg/json/issues/274  

`to_string` can eliminate some or all memory reallocations by first serializing to a local stack buffer, then copying the result to the output string

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-07 18:45:38 UTC  
> Url: https://github.com/boostorg/json/issues/274#issuecomment-688471633  

Done, and the function is now called `serialize()`
