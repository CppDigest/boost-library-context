# #439 - Review: optional values in query parameter API [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 05:35:18 UTC  
> Updated at: 2022-09-11 23:46:54 UTC  
> Closed at: 2022-09-11 23:46:53 UTC  
> Url: https://github.com/boostorg/url/issues/439  

> In your query parameter API, you expose something that looks like a  
standard associative container except that you call the key and value  
"key" and "value" respectively, rather than "first" and "second".  
While I think many of us would agree that those are better names, it  
does make it more difficult to use this container with generic code  
than it would be otherwise.  
>  
> I see that you also have a bool called "has_value" in the value_type.  
I think this is to handle this:  
>  
>  ?a=1   // has_value == true && value == "1"  
>  ?a=    // has_value == true && value == ""  
>  ?a     // has_value == false  
>  
> Is that correct? Again having this third member in the container's  
value_type makes generic code more difficult. I would be inclined to  
use a std::optional for the value instead.  
  
There are three options I see so far:  
- Do nothing  
- Use std::optional for the value.  
- Encapsulate the values in a class and return them one way or the other. Whatever we choose becomes an implementation detail.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:46:53 UTC  
> Url: https://github.com/boostorg/url/issues/439#issuecomment-1243070627  

optional sucks, because you MUST check if it is engaged. in the current scheme, you can get away with checking only if the value is empty most of the time. so optional is strictly worse.
