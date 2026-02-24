# #160 - const std::string return value results in pessimization [Closed]

> Username: kauboy26  
> Created at: 2020-09-23 01:49:15 UTC  
> Updated at: 2020-09-23 17:03:04 UTC  
> Closed at: 2020-09-23 17:02:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/160  

Hi,  
  
I was taking a look at path.hpp and found that some functions, such as [`path::string()`](https://github.com/boostorg/filesystem/blob/d855c2d37734262e3db5fa938c384d16769b8f05/include/boost/filesystem/path.hpp#L435), returned by value but were marked as const.  
  
So for example, if I had a path object `path1`, and then I made a `std::string` object `s` as follows:    
```    
std::string s = path1.string();    
```    
The above would call `std::string`'s copy constructor rather than its move constructor.  
  
I was wondering if this was intended behavior, as we are missing a chance to optimize.  
  
Thank you!

---

## Comment 1

> Username: Lastique  
> Created at: 2020-09-23 17:03:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/160#issuecomment-697704145  

Thanks for the report.
