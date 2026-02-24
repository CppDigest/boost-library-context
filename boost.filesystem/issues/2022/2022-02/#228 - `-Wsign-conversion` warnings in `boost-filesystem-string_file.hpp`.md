# #228 - `-Wsign-conversion` warnings in `boost/filesystem/string_file.hpp` [Closed]

> Username: cmazakas  
> Created at: 2022-02-08 17:38:59 UTC  
> Updated at: 2022-02-08 18:11:51 UTC  
> Closed at: 2022-02-08 18:11:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/228  

It seems like the `string_file.hpp` header will generate sign conversion warnings:  
```cpp  
In file included from test/unordered/scoped_allocator.cpp:25:  
In file included from ../../boost/filesystem.hpp:22:  
../../boost/filesystem/string_file.hpp:30:33: warning: implicit conversion changes signedness: 'std::basic_string<char>::size_type' (aka 'unsigned long') to 'std::streamsize' (aka 'long') [-Wsign-conversion]  
    file.write(str.c_str(), str.size());  
         ~~~~~              ~~~~^~~~~~  
../../boost/filesystem/string_file.hpp:40:24: warning: implicit conversion changes signedness: 'std::size_t' (aka 'unsigned long') to 'std::streamsize' (aka 'long') [-Wsign-conversion]  
    file.read(&str[0], sz);  
```  
  
The warning was generated from clang-12 for both C++03 and C++20

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-08 17:43:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/228#issuecomment-1032888053  

These convenience functions should probably be moved out of line anyway.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-02-08 18:05:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/228#issuecomment-1032909023  

I'm going to deprecate and remove them instead. These are out of scope of the library.
