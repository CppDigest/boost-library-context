# #61 - ambiguous overload for 'operator==' [Closed]

> Username: dimarusyy  
> Created at: 2021-01-20 09:21:17 UTC  
> Updated at: 2021-12-09 17:11:15 UTC  
> Closed at: 2021-12-09 17:11:15 UTC  
> Url: https://github.com/boostorg/system/issues/61  

Applying `is_error_code_enum` for std::error_code emits compilation error:  
https://godbolt.org/z/hzEP9n  
  
This is a regression since boost-1.65.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-01-20 16:08:10 UTC  
> Url: https://github.com/boostorg/system/issues/61#issuecomment-763739195  

An interesting technique.  
  
At this point I think I can only suggest a workaround on your side: define `operator==(boost::system::error_code e1, std::error_code e2)` with your desired semantics.  
  
Boost.System can define this overload, but it won't have the behavior you want because it will convert e1 to std::error_code, not e2 to boost::system::error_code, and this won't match the pre-1.66 behavior of the comparison.
