# #281 - Deviation from the standard [Closed]

> Username: d0nc1h0t  
> Created at: 2023-03-27 07:17:50 UTC  
> Updated at: 2023-03-27 10:29:08 UTC  
> Closed at: 2023-03-27 10:29:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/281  

The current version does not support auto-conversion of different types of characters (char, char16_t, char32_t, wchar_t) in accordance with the standard. But now they have also banned writing their own converters via path_traits::is_convertible_to_path_source, the verification of which was removed on the constructor of the path class. Construction is allowed only from native strings. The way of development of the library is absolutely incomprehensible.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-03-27 10:29:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/281#issuecomment-1484898468  

The conversion of `char` and `wchar_t` is supported in `path` constructors, including `std::string` and various other string types. I'm not sure what you're referring to.  
  
`char16_t` and `char32_t` are not supported yet, this is already reported in https://github.com/boostorg/filesystem/issues/86.  
  
If you have a specific problem, please report it with a reproducer.
