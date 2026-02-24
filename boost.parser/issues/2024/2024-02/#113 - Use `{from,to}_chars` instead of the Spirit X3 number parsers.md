# #113 - Use `{from,to}_chars` instead of the Spirit X3 number parsers. [Closed]

> Username: tzlaine  
> Created at: 2024-02-24 00:42:38 UTC  
> Updated at: 2024-03-15 21:29:28 UTC  
> Closed at: 2024-03-15 21:29:28 UTC  
> Url: https://github.com/boostorg/parser/issues/113  

This could be `std::` or `boost::charconv::`, whichever is available.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-26 06:05:49 UTC  
> Url: https://github.com/boostorg/parser/issues/113#issuecomment-1963381762  

I'm less hot on this idea after looking at `from_chars`:  
  
```c++  
std::from_chars_result  
    from_chars( const char* first, const char* last,  
                /* integer-type */& value, int base = 10 );  
```  
  
So for this to work, the parse input would need to be a `common_range`.  Additionally, it would need to be `char`s, or a transcoding range that has `char`s at bottom.  
  
Also, it would only have the same behavior when `MinDigits` and/or `MaxDigits` are not in use.  
  
Ok, I guess it's not that limiting.  I guess most input will be in `char`s, transcoded or not.
