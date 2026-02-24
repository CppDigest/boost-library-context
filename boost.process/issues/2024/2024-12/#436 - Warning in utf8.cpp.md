# #436 - Warning in utf8.cpp [Closed]

> Username: anarthal  
> Created at: 2024-12-07 10:33:03 UTC  
> Updated at: 2024-12-13 03:58:25 UTC  
> Closed at: 2024-12-13 03:58:24 UTC  
> Url: https://github.com/boostorg/process/issues/436  

This line issues a warning on an unused `char_count`: https://github.com/boostorg/process/blob/fa83c2f9ffcd3bbdfa9244c777b64e3ca98c07c4/src/detail/utf8.cpp#L213  
  
You may replace it with a while.
