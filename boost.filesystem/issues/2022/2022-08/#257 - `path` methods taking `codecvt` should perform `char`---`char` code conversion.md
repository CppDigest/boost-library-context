# #257 - `path` methods taking `codecvt` should perform `char`<->`char` code conversion [Open]

> Username: Lastique  
> Created at: 2022-08-31 06:18:51 UTC  
> Updated at: 2022-08-31 06:18:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/257  

Currently, constructor/assignment/appending methods that accept `codecvt_type` arguments only perform character code conversion when the source character type does not match `path::value_type`. C++ standard requires (e.g. [here](http://eel.is/c++draft/filesystems#fs.path.construct-6) and [here](http://eel.is/c++draft/filesystems#fs.path.construct-7)) that when both `path::value_type` and source value type are `char` the conversion is performed twice: first, from source encoding to `wchar_t` using the supplied `codecvt_type` facet, then from `wchar_t` to native path character encoding using `path::codecvt()`.
