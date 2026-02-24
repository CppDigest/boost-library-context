# #24 fix compile error on basic_string_view::to_string when Allocator is user-defined [Closed]

> Username: gnaggnoyil  
> Created at: 2016-08-26 02:35:18 UTC  
> Updated at: 2016-09-01 19:45:19 UTC  
> Closed at: 2016-09-01 19:45:19 UTC  
> Url: https://github.com/boostorg/utility/pull/24  

I guess it's a typo, but currently the return type of boost::basic_string_view< C, T, A>::template to_string< A1 >() is std::basic_string< C, T, std::allocator< C >>, while the return type inferred from the return statement is std::basic_string< C, T, A1> which I guess it is the original intended return type.  
  
This patch is meant to fix this "typo" (if it really is) so that to_string can return any basic_string types using different allocators. Also, I added a testcase in string_view_test2.cpp to prevent this typo from happening again.

---

## Comment 1

> Username: Lastique  
> Created_at: 2016-09-01 19:45:19 UTC  
> Url: https://github.com/boostorg/utility/pull/24#issuecomment-244190373  

Merged to develop in e5932ebb08ecc462faaf96a53b55c921a2d83823, 4814d1ebfeb7aab20722b54aa04b16a84b517f00 and 3d853b0e83e4af8b7af8fac1fde0229469733305.

---
