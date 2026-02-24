# #1065 - Document getting source_locations [Open]

> Username: computerquip-work  
> Created at: 2025-01-08 18:54:09 UTC  
> Updated at: 2025-01-09 08:19:12 UTC  
> Url: https://github.com/boostorg/json/issues/1065  

I was struggling a bit getting reasonable error codes. I use the exception-based API for readability purposes. Most of these functions take a source_location parameter but it turns out that the location information is lost when the exception is thrown. For example a call to `at()` on a `json::object` will drop the information and instead have the call site of the internal function:  
```  
value const&  
object::  
at(string_view key, source_location const& loc) const&  
{  
    return try_at(key).value(loc);  
}  
```  
`try_at` gets called and throws so `loc` being set in the result type doesn't matter.  
Is there a decent way to avoid this short of wrapping everything?

---

## Comment 1

> Username: computerquip-work  
> Created at: 2025-01-08 20:27:16 UTC  
> Updated at: 2025-01-08 20:28:48 UTC  
> Url: https://github.com/boostorg/json/issues/1065#issuecomment-2578593319  

Welp, I'm wrong here. There's two source_location objects being stored, one in the ec and one in the location_ over in throw_location. I thought the macro in try_at conditionally threw if exceptions were enabled but I was obviously wrong. In order to get the correct throw location, seems a call to get_throw_location has to be made. I guess I learned a bit more about boost exceptions today. Sorry fro the clutter.

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-01-09 08:18:49 UTC  
> Url: https://github.com/boostorg/json/issues/1065#issuecomment-2579413767  

> Sorry fro the clutter.  
  
No problem. I should really document how to get the corresponding `source_location`s. The fact that you were confused by them highlights it. I'm reopening the issue and repurposing it for tracking documentation for dealing with `source_location`s.
