# #193 - Remove implicit trailing dot filename [Closed]

> Username: Lastique  
> Created at: 2021-06-06 15:53:21 UTC  
> Updated at: 2021-10-17 21:45:09 UTC  
> Closed at: 2021-10-17 21:45:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/193  

This was originally reported in https://github.com/boostorg/filesystem/issues/88.  
  
Unlike C++17 std::filesystem, Boost.Filesystem currently adds an implicit dot filename, even when one is not present in the path. This is primarily done when the path ends with a directory separator, to indicate a non-empty filename that is inside the directory. This affects `path::filename`, `path` iterators and lexical normalization.  
  
It was suggested to remove this behavior and report an empty filename, as std::filesystem does.
