# #222 - Deprecate I/O interface using boost::filesystem::path [Open]

> Username: mloskot  
> Created at: 2019-01-21 21:18:34 UTC  
> Updated at: 2022-05-20 09:15:03 UTC  
> Url: https://github.com/boostorg/gil/issues/222  

TL;TR: We are planning to deprecate functions accepting `boost::filesystem::path` and replace with C++17 `std::filesystem`   
  
## What  
  
Currently, Boost.Filesystem support is optional, guarded with macro [BOOST_GIL_IO_ADD_FS_PATH_SUPPORT](https://github.com/search?utf8=%E2%9C%93&q=BOOST_GIL_IO_ADD_FS_PATH_SUPPORT+repo%3Aboostorg%2Fgil+extension%3Ahpp+language%3AC%2B%2B+language%3AC%2B%2B&type=Code&ref=advsearch&l=C%2B%2B&l=C%2B%2B) checked in number of the I/O extension headers.  
  
It is reasonable to replace the `boost::filesystem` with `std::filesystem` whenever GIL code is compiled using C++17,  
unless user explicitly desires to use Boost.Filesystem via an appropriate `#define`.  
  
## Why  
  
1. We, @stefanseefeld  & @mloskot, don't see any added value in function overloads such as `make_dynamic_image_reader` that use `filesystem::path` over just passing `std::string` or `char const*` and leaving the conversion from the `path` to `string` to the converting constructors or user.  
  
2. Avoid Boost.Filesystem as direct and mandatory dependency.  
  
Briefly, we considered automatically replacing `boost::filesystem` with `std::filesystem`, depending on compiler and target C++ version, but we quickly landed at the point 1. Plus, the `<filesystem>` detection heuristics would not be as straightforward as one may expect.  
  
## When  
  
As soon as possible - along with complete C++11 switch

---

## Comment 1

> Username: mloskot  
> Created at: 2022-02-03 22:46:47 UTC  
> Url: https://github.com/boostorg/gil/issues/222#issuecomment-1029472030  

I've updated this issue with C++17 `std::filesystem`:  
  
> It is reasonable to replace the boost::filesystem with std::filesystem whenever GIL code is compiled using C++17,  
unless user explicitly desires to use Boost.Filesystem via an appropriate #define.

---

## Comment 2

> Username: mloskot  
> Created at: 2022-05-20 09:15:03 UTC  
> Url: https://github.com/boostorg/gil/issues/222#issuecomment-1132671071  

This is included in the planning towards C++14/17 discussion here https://github.com/boostorg/gil/discussions/676
