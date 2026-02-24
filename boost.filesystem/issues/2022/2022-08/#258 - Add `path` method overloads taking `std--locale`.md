# #258 - Add `path` method overloads taking `std::locale` [Open]

> Username: Lastique  
> Created at: 2022-08-31 06:25:34 UTC  
> Updated at: 2022-08-31 06:25:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/258  

`std::filesystem::path` [passes](http://eel.is/c++draft/filesystems#fs.path.construct-6) `std::locale` objects as opposed to `codecvt` facets. We can support both, using `std::use_facet<codecvt_type>(loc)`.
