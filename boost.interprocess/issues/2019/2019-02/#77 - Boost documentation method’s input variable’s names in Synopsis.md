# #77 - Boost documentation method’s input variable’s names in Synopsis [Closed]

> Username: mathbunnyru  
> Created at: 2019-02-15 20:06:31 UTC  
> Updated at: 2026-01-07 09:39:25 UTC  
> Closed at: 2026-01-07 09:39:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/77  

Why does documentation omits method’s input variable’s names in `Synopsis` section?  
  
For example:  
https://www.boost.org/doc/libs/1_69_0/doc/html/boost/interprocess/mapped_region.html  
  
```  
class mapped_region {  
...  
  bool flush(std::size_t = 0, std::size_t = 0, bool = true);  
...  
}  
```  
  
This looks bad for me, because there’s no way to understand the meaning of `size_t`s or `bool`. I think this synopsis doesn’t help as much as it could.  
  
Hopefully, there is more appropriate description below:  
  
```  
bool flush(std::size_t mapping_offset = 0, std::size_t numbytes = 0,   
           bool async = true);  
```  
  
I think it would be good to have this in synopsis.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 09:39:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/77#issuecomment-3718042405  

This is automatically created by the Boost documentation system, sorry, there is no way to change it, although I agree that it would be nice to have variable names in the synpsis.
