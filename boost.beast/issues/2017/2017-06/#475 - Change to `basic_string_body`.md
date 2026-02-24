# #475 - Change to `basic_string_body` [Closed]

> Username: vinniefalco  
> Created at: 2017-06-13 01:22:53 UTC  
> Updated at: 2017-06-24 15:55:10 UTC  
> Closed at: 2017-06-24 15:55:10 UTC  
> Url: https://github.com/boostorg/beast/issues/475  

```  
template<  
    class Char,  
    class CharT = std::char_traits<CharT>,  
    class Allocator = std::allocator<Char>>  
struct basic_string_body;  
  
using string_body = basic_string_body<char>;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-24 15:55:10 UTC  
> Url: https://github.com/boostorg/beast/issues/475#issuecomment-310846940  

On further reflection, a proliferation of similar body types is not in the best interests of the library. This conversation was a motivator:  
https://github.com/vinniefalco/Beast/issues/536
