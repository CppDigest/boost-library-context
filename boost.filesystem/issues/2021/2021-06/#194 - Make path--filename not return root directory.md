# #194 - Make path::filename not return root directory [Closed]

> Username: Lastique  
> Created at: 2021-06-06 16:03:03 UTC  
> Updated at: 2021-06-10 01:24:05 UTC  
> Closed at: 2021-06-10 01:24:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/194  

This was originally reported in https://github.com/boostorg/filesystem/issues/88.  
  
Unlike C++17 std::filesystem, `path::filename` returns root directory, if the path ends with the root directory. For example, `path("/").filename() == "/"`, while C++17 mandates an empty filename in this case.  
  
It is not clear why this was made this way, but it makes sense to change this behavior to make it closer to std::filesystem.
