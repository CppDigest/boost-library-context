# #220 - Consider adding a function to `uint_parser`/`int_parser` that aids construction of custom integral parsers [Closed]

> Username: tzlaine  
> Created at: 2025-04-12 19:02:02 UTC  
> Updated at: 2025-04-13 01:04:48 UTC  
> Closed at: 2025-04-13 01:04:48 UTC  
> Url: https://github.com/boostorg/parser/issues/220  

Right now, to make a non-base-10, or digit-limited integral parser, you have to write something like this (from the JSON example):  
  
```c++  
constexpr parser_interface<uint_parser<unsigned int, 16, 8, 8>> hex_int;  
```  
  
This is problematic, since we don't really want to require users to use `parser_interface` directly.  We should add a member function on `*int_parser` that might replace that.  For instance:  
  
```c++  
p = uint.base<16>().digits<8, 8>();  
```  
  
... or similar.
