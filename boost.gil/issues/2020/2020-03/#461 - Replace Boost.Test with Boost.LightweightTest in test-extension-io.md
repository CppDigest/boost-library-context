# #461 - Replace Boost.Test with Boost.LightweightTest in test/extension/io [Closed]

> Username: mloskot  
> Created at: 2020-03-21 19:18:28 UTC  
> Updated at: 2020-04-17 17:27:00 UTC  
> Closed at: 2020-03-24 09:21:20 UTC  
> Url: https://github.com/boostorg/gil/issues/461  

This task is continuation of #459 but in `test/extension/io`  
  
- Move test sources to format-specific directories (e.g. `test/extension/io/jpeg_*.cpp` to `test/extension/io/jpeg/*.cpp`)  
- Refactor each `.cpp` file as separate test with `main` using Boost.LT  
- Restructure targets in `Jamfile` and `CMakeLists.txt` files to catch up with the new folders layout
