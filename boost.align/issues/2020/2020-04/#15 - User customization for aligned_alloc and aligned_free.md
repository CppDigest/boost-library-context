# #15 - User customization for aligned_alloc and aligned_free [Closed]

> Username: glenfe  
> Created at: 2020-04-30 12:22:54 UTC  
> Updated at: 2020-05-12 18:52:49 UTC  
> Closed at: 2020-05-12 18:52:49 UTC  
> Url: https://github.com/boostorg/align/issues/15  

Users have needs such as being able to customize which allocation function `aligned_alloc` uses. Currently it detects the best available option on the given implementation and provides a fallback implementation which uses `std::malloc` in combination with `std::align`. We will do all of the following:  
* Allow users to pick a specific implementation via a macro  
* Provide an additional implementation where alignment-aware `operator new` is available  
* Allow users to choose which fallback allocation function is used (e.g. instead of `std::malloc(std::size_t)` users could choose to have `::operator new(std::size_t, const std::nothrow_t&)`
