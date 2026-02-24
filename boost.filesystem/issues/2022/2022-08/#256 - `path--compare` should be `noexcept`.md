# #256 - `path::compare` should be `noexcept` [Open]

> Username: Lastique  
> Created at: 2022-08-31 05:56:22 UTC  
> Updated at: 2022-08-31 06:57:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/256  

`path::compare` calls `lex_compare_vN` internally, which operates on `path::iterator`s and therefore is not `noexcept`. C++ standard says `path::compare` should be `noexcept`.
