# #48 - Parser does not respect null terminator [Closed]

> Username: mborland  
> Created at: 2023-05-23 14:21:20 UTC  
> Updated at: 2023-05-24 13:46:06 UTC  
> Closed at: 2023-05-24 13:46:06 UTC  
> Url: https://github.com/boostorg/charconv/issues/48  

The benchmark file provides a long string of values with [`\0` in between them](https://github.com/cppalliance/charconv/pull/44/files#diff-580e4dccc985f9bd990100d76c9bb7b61bda8e9fe19afea91fd0852e566c35a1R338). The parser currently processes this as a valid character.
